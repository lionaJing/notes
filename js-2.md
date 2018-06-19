# React 相关

了解React之前先看React [whay React](https://www.jianshu.com/p/ae482813b791) [阮一峰 React 技术栈](http://www.ruanyifeng.com/blog/2016/09/react-technology-stack.html)

React 的组件都有自己的组件状态,通过与用户的交互,实现不同状态,然后渲染 UI,让用户界面和数据保持一致。(只需更新组件的 state,
然后根据新的 state 重新渲染用户界面,不直接操作DOM对象，而是通过虚拟DOM通过diff算法以最小的步骤作用到真实的DOM上)

```
var Button = React.createClass({
  getInitialState: function() {
	/* 默认状态 */
	return {
		data:0
	};
  },
  getDefaultProps: function() {
	/* 默认属性 */
	return {
		name: 'Jack'
	};
  },
  /* 点击事件 */
  setNewNumber: function() {
	this.setState({data: this.state.data + 1})
  },
  render: function () {
	  return (
		 <div>
			<p>{this.props.name}</p>
			<button onClick = {this.setNewNumber}>点击 + 1</button>
			<Content myNumber = {this.state.data}></Content>
		 </div>
	  );
	}
});
var Content = React.createClass({
	componentWillMount:function() {
		console.log('在渲染前调用,在客户端也在服务端')
	},
	componentDidMount:function() {
		console.log('在第一次渲染后调用,只在客户端,组件已经生成了对应的DOM结构,可以进行ajax请求x ')
	},
	componentWillReceiveProps:function(newProps) {
		console.log('在组件接收到一个新的 prop (更新后)时被调用。这个方法在初始化render时不会被调用')
	},
	shouldComponentUpdate:function(newProps, newState) {
		console.log('返回一个布尔值。在组件接收到新的props或者state时被调用。在初始化时或者使用forceUpdate时不被调用');
		return true;
	},
	componentWillUpdate:function(nextProps, nextState) {
		console.log('在组件接收到新的props或者state但还没有render时被调用');
	},
	componentDidUpdate:function(prevProps, prevState) {
		console.log('在组件完成更新后立即调用')
	},
	componentWillUnmount:function() {
		console.log('在组件从 DOM 中移除的时候立刻被调用')
	},

	render: function () {
		return (
			<div>
				<h3>{this.props.myNumber}</h3>
			</div>
		);
	}
});
// 渲染
ReactDOM.render(
	<div>
		<Button />
	</div>,
	document.getElementById('example')
);

```

## Redux

[Redux 中文文档](http://www.redux.org.cn/) [阮一峰Redux技术系列](http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html)

Redux 是一个应用数据流框架,作用是对组件状态的管理

* Store
一个应用只有一个Store(状态树),它提供的唯一数据源，它存储了整个应用的state, store 是只读的，redux没有提供直接
修改数据的方法，改变state的唯一方法就是触发（dispatch） action 

* action
是普通的 JS 对象,必须要有 type 属性,它是对行为的抽象(即包含用户对页面的操作)
```
action: 
{
  type: 'ADD_ITEM',
  payload: 'add user'
}

// 通常需要一个函数来创建(返回) action
function addUser(text) {
	return: {
		type: 'ADD_ITEM',
		text
	}
}

//在应用中调用 Action
dispatch(addUser(text));
```

* reducer
负责对 action 描述的行为进行执行,只接收 state和action 作为参数,并返回新的state的函数(纯函数,即不能执行其他操作,
只能根据输入得到指定的输出结果)
