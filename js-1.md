
### js 清空数组
```
let array = [1,2,3,4,5,6];
array.splice(0,array.length);
```

### js 合并数组

* concat():
```
let array1 = [1,2,3,4,5];
let array2 = [6,7,8,9,10,11,12,13];
let array3 = array1.concat(array2);
```
concat 合并两个数组后会返回一个新数组，多次合并造成内存浪费

* for循环
```
for(let i of array) {
	array1.push(i);
}
array = null;
```
没有造成上面的内存浪费(unshift 从头部插入)

* Array.reduce(or reduceRight)
```
let array1 = [1,2,3,4,5,6];
let array2 = [7,8,9,10,11,12];
//reduce((*arg1,*arg2,arg3,arg4),arg5)
//arg1: 计算后的返回值
//arg2: 当前元素
//arg3: 当前元素索引
//arg4: 当前数组对象
//arg5: 传递给函数的初始值
array1.reduce(function(m,n){
	m.push(n);
    return m;
},array2);
```

* apply
```
var array = [1,2,3,4,5,6,7];
var array2 = [8,9,10,11,12,13];
array.push.apply(array,array2);
```

### 原生 js 增加移除样式

```
<style>
	.Boy {
		red: red;
	}
</style>

let p = document.getElementById("demo");
p.classList.add("Boy");
p.classList.remove("Boy");
```

### 原生 js 点击事件

```
let btn = document.getElementById("demo");
btn.onclick=function(){
	alert('hello world');
}

btn.addEventListener("click",function() {
	console.log("hi...");
},false);
```

### 监听 input 输入值

汇总onchange onpropertychange 和oninput事件的区别：
onchange触发事件必须满足两个条件：
* 当前对象属性改变，并且是由键盘或鼠标事件激发的（脚本触发无效）
* 当前对象失去焦点(onblur)；
onpropertychange的话，只要当前对象属性发生改变，都会触发事件，但是它是IE专属的；
oninput是onpropertychange的非IE浏览器版本，支持firefox和opera等浏览器，但有一点不同，它绑定于对象时，
并非该对象所有属性改变都能触发事件，它只在对象value值发生改变时奏效。

```
function OnInput (event) {
	//Firefox, Google Chrome, Opera, Safari, Internet Explorer from version 9
	alert ("The new content: " + event.target.value);
}
function OnPropChanged (event) {
	//Internet Explorer
	if (event.propertyName.toLowerCase () == "value") {
		alert ("The new content: " + event.srcElement.value);
	}
}

<input type="text" oninput="OnInput (event)" onpropertychange="OnPropChanged (event)" value="Text field" />

```

### 原生 js 获得父结点、子节点、兄弟结点
```
var a = document.getElementById("dom");
var b = a.childNodes;//获取a的全部子节点；
var c = a.parentNode;//获取a的父节点；
var d = a.nextSibling;//获取a的下一个兄弟节点
var e = a.previousSibling;//获取a的上一个兄弟节点
var f = a.firstChild;//获取a的第一个子节点
var g = a.lastChild;//获取a的最后一个子节点
```
注意空格/换行符

### 原生 js 获取 P 标签的值
```
var p = document.getElementById("mP");
p.innerText;
p.innerHTML;
```

### js String2number、number2String
```
let number1 = parseInt("100.12.23");
//输出：100
let number2 = parseFloat("100.12.23");
//输出：100.12
//parseInt 遇到小数点停止解析,parseFloat 第一个小数点有效


let str1 = 100 + "";
let str2 = 100.toString();
let st3 = new Number(100); //将对象转为数字
//Number true和false值将分别被转换为1和0
//如果是null值，返回0。
//如果是undefined，返回NaN
//如果字符串为空，将其转换为0
//如果字符串包含数字+字符，返回NaN
```

### 用 JavaScript 准确获取手机屏幕的宽度和高度
```
网页可见区域宽：document.body.clientWidth 
网页可见区域高：document.body.clientHeight 
网页可见区域宽：document.body.offsetWidth (包括边线的宽) 
网页可见区域高：document.body.offsetHeight (包括边线的宽)
网页正文全文宽：document.body.scrollWidth 
网页正文全文高：document.body.scrollHeight
网页被卷去的高：document.body.scrollTop
网页被卷去的左：document.body.scrollLeft 
网页正文部分上：window.screenTop 
网页正文部分左：window.screenLeft
屏幕分辨率的高：window.screen.height 
屏幕分辨率的宽：window.screen.width
屏幕可用工作区高度：window.screen.availHeight
屏幕可用工作区宽度：window.screen.availWidth
```

### baidu map js版去除左下角的LOGO

1. 在页面中新增: setInterval(function(){$(".anchorBL").remove();},50);
2. 在页面中增加样式: .anchorBL{display:none;};

### JavaScript 模块化开发

* CommonJS(node.js) 服务端规范， 同步执行
* CMD规范(require.js) 浏览器规范, 异步执行(依赖前置，在定义模块的时候就要声明其依赖的模块)
* CMD规范(SeaJS) 浏览器规范, 异步执行(就近依赖，只有在用到某个模块的时候再去require)
* ECM6规范：export和import

### ECM6 箭头函数

没有返回值 `() => {}`
返回对象 `() => ()`

## Set Map

Set: 一组key的集合,但不存储value,没有重复的key
```
//set 转化为 Array
let set = new Set([1,2,3,4]);
Array.from(set)

//遍历 set
for(var i for set) {
	console.log(i); //1,2,3,4
}
set.forEach(function (element, sameElement, set) {
	console.log(element+","+sameElement);
	//1,1 2,2 3,3 4,4
});
//Set 的容量
set.size
```
Map: 键值对的结构,一个key只能对应一个value,具有极快的查找速度
```
let map = new Map();
map.set("1","a");
map.set("2",{"name","jack"});
map.get("2");
map.has("2"); //返回 true false
map.delete("2");
//遍历 map
for (var x of map) { // 遍历Map
  console.log(x[0] + '=' + x[1]);
}
map.forEach(function (key, value, map) {
	console.log(key+","+value);
});
//Map 的容量
map.size
```

## 打印 js Obj 所有属性

console.dir(obj)

## js 解析 URL

```
function getQueryString(name) {
    var reg = new RegExp('(^|&)' + name + '=([^&]*)(&|$)', 'i');
    var r = window.location.search.substr(1).match(reg);
    if (r != null) {
        return unescape(r[2]);
    }
    return null;
}
getQueryString("参数名");
//www.baidu.com?where=1
//getQueryString("where");
```






