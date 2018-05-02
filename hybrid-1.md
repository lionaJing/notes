## 列表点击事件失效?

vue + mui 做移动端项目是遇到的vue v-for循环添加 DOM 结点,li点击事件无效的问题,调试时在PC端调试,点击列表，点击事件正常，
但在移动端(Android)无法正常响应.

vue的点击事件：
vue通过指令进行事件绑定(添加事件修饰符)，v-on:click(@click)
mui支持的手势事件：
点击(top...)、长按(longtap...)、滑动(swip...)、拖动(drag...)

mui不直接支持click事件，所以在 v-for情况下点击事件失效（至于PC 浏览器调试跳转正常，原因未知...）,

解决：
当 DOM 准备就绪时（即 vue 的 beforeMount 钩子函数），使用 MUI,对需要响应的Element进行 top 事件监听，让它去调用
自己的 click 事件.

```
mui.ready(function() {
	mui("#Ele").on('tap', 'li', function (event) {
		this.click();
	});
});

```

当该li里面存在其他点击事件时，回存在事件冒泡,解决方法:

```
mui("#refreshContainer").on('tap', '#phone', function (event) {
	this.click();
	event.stopPropagation();
});

```

## vue 监听 input 值变化

* 方法1
```
<input type="text" v-on:input ="onInputListener(event,obj)"/>
```
缺点：只能在手动输入/删除 input 值时有效,通过程序改变 input 值无法监听

* 方法2,使用 v-model + watch

```
<input class="mui-numbox-input" type="number" v-model="msg"/>
<input class="mui-numbox-input" type="number" v-model="obj.name"/>

var vue = new Vue({
	data:{
		msg: "",
		obj: {
			name:""
		}
	},
	watch: {
		//普通监听
		msg:function(val,old) {
			console.log(val+","+old);
		},
		//对于对象，数组需要使用深度监听
		obj:{
			handler:function(val,old) {
				console.log(val.name+","+old.name);
			},
			deep:true
		}
	}
});
```
缺点：只能在手动输入/删除 input 值时有效,通过程序改变 input 值无法监听

### 针对点击按钮改变input的值，对该值进行监听(vue + 原生js)

```
<div>
	<button @click="toPlusValue(event)">+</button>
	<input type="text"/>
</div>

function toPlusValue(event) {
	//得到它的父节点，在通过父节点得到子节点列表
	//不知道为什么通过兄弟结点总是 null
	//应该是空格换行的原因，需要处理
	var input = that.target.parentNode.childNodes;
	//[0]:button [1]:text(+) [2]:input
	onsole.log("值 = "+input[2].value);
}
```

### mui + vue 的数字输入框存在的问题

在 vue 中,给 button 绑定点击事件(如下),如果设置了最小值(比如为 0),同时给 
button 设置点击事件监听，当输入框值为0是，再去点击 button 点击事件不触发。
但是如果使用 mui 进行监听,点击事件正常。

原因：mui 是将 点击事件重写了的（top）,不同于 vue 的 click.出现这种情况我的
解释是：执行 button top 事件 -> input value 值改变(根据最小值,决定 button
的 disable ) -> vue 的点击事件。
注意： 在 mui 的 top 事件里如果要得到当前的 value,那么是变化之前的值

解决: 不去设置 最大、最小值。自己实现 button 的 click 事件(vue绑定，因为 mui
的 top 事件中获得的是变化之前的值),然后根据获得的值与期望最小值比较，如果
<= 0,设置当前 button 的 disable = true。

```
<button type="button" v-on:click="toSubInputValue(event)">-</button>
	<input type="number" v-on:input ="onInputListener(event)"/>
<button type="button" v-on:click="toPlusInputValue(event)">+</button>
```

### 在vue中获取dom元素

在vue中可以通过给标签加ref属性，就可以在js中利用ref去引用它，
从而操作该dom元素，以下是个例子，可以当做参考

```
<template>  
  <div>  
    <div id="box" ref="mybox">  
      DEMO  
    </div>  
  </div>  
</template>  
  
<script>  
export default {  
  data () {  
    return {  
        
    }  
  },  
  mounted () {  
    this.init();  
  },  
  methods:{  
    init() {  
      const self = this;  
      this.$refs.mybox.style.color = 'red';  
    }  
  }  
  
}  
</script> 
```

### 在vue中获取自定义属性值

假设有一个标签h5, 我们给它添加了一个自定义属性值，（item.id是从动态添加的）
点击h5 标签，如何才能获取当前对应的自定义属性值呢？
```
<h5 @click='getDataId(item.id)' :data-id="item.id"></h5>

<script>
    methods: {
		getDataId(id) {
            console.log(id);
        }
    },
    
</script>
```