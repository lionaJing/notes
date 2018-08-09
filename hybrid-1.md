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

## 针对点击按钮改变input的值，对该值进行监听(vue + 原生js)

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

## mui + vue 的数字输入框存在的问题

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

## 在vue中获取dom元素

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

## 在vue中获取自定义属性值

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

## mui 样式

mui-pull-right mui-pull-left mui-text-right mui-text-left mui-ellipsis-2(数字2可变)
mui-media(在列表li上设置则列表有点击效果)
`<a class="mui-navigate-right"></a> 向右的箭头`

## ajax 请求遇到报 parsererror 错误(error)

先检查 XMLHttpRequest、textStatus
```
error:function(XMLHttpRequest, textStatus, errorThrown) {
	console.log("textStatus 错误信息: "+textStatus);
	console.log("状态码: "+XMLHttpRequest.status);
	//打印
	//textStatus 错误信息: parsererror
	//状态码: 200(客户端请求已成功)
}

原因分析：
1. 如果返回值是 json,看它的引号是单引号还是双引号
2. 返回值不合法，服务端的问题
3. 和Header 类型也有关系。及编码header('Content-type: text/html; charset=utf8')

```

## ajax(jQuery) POST 请求传参为 json
```
$.ajax({
	url: "",
	data: JSON.stringify({name:"jack",pwd:"***"});
	type:"POST",
	
	//根据情况而定，表示接收的数据类型
	//json: 把获取到的数据作为一个JavaScript对象来解析，并且把构建好的对象作为结果返回
	//text、xml: 直接显示字符串，text和xml类型返回的数据不会经过处理
	//html：内嵌的JavaScript都会在HTML作为一个字符串返回之前执行
	//script：会先执行服务器端生成JavaScript，然后再把脚本作为一个文本数据返回
	dataType: "json", 
	
	//发送信息至服务器时内容编码类型
	//默认值: "application/x-www-form-urlencoded"
	contentType: "application/json; charset=utf-8",
	
	success:function(data){},
	error:function() {}
})
```

## vue 点击事件传递当前元素
```
v-on:click="selectItem($event)"

selectItem:function(event) {
	var e = event.currentTarget;
	console.log(e.innerHTML);
}
```

## vue2.X 列表循环传递当前索引
```
<item v-for="obj,index in list">
	<a v-on:click="onSelectItem(obj,index)"></a>
</item>
//参数index需写在item后面
//不用加 $
```