
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