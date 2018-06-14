# 一些 Hybrid 开发框架技术笔记

## Dcloud(HBuilder)

纯js搞定一切,使用起来比较简单,只有使用他们提供的 IDE(HBuilder) 才能使用其特性,HBuilder 里封装了 Native.js、HTML5+,
由于 IOS、Android 代码格式不同所以在调用原生使用时 必须进行运行平台的判断 `plus.os.name` 然后才能使用
对应系统的 api(通过 js 调用); UI 风格使用的是 MUI(样式还可以,可以单独拉出使用),开发 IDE HBuilder,可以
同时打包(云打包)Android、IOS

Dcloud 没有做成插件的形式，而是使用 Nativejs 做了原生代码 到 js 的映射(IOS、Android 分开调用)
在编写具体H5页面时一般采用 MUI + vue 形式开发

[文档](http://ask.dcloud.net.cn/docs/#//ask.dcloud.net.cn/article) [MUI](http://dev.dcloud.net.cn/mui/) [html5+ 和phoneGap、cordova的比较](https://blog.csdn.net/qq449245884/article/details/54100005)

## Framework7

一款混合移动应用开发框架,主打使用 HTML、CSS和JS 开发应用(并不能兼容所有的设备.只专注于为 iOS 和
Google Material 设计提供最好的体验).与原生的交互需要依赖插件(cordova)
[中文官网](http://www.framework7.cn/)

使用方式：
* 直接引用 Framework7 css、js
[地址](http://www.framework7.cn/docs/introduction.html)
[下载地址](https://github.com/framework7io/framework7/releases)
从下载好的压缩包里的目录 dist/css 和 dist/js 可以找到我们需要的所有文件

* Framework7 + vue
[地址](http://www.framework7.cn/vue/) [Framework7+Framework7-vue+vue踩坑记](https://www.jianshu.com/p/9c0d0702a2aa)

* Framework7 + react
[地址](http://www.framework7.cn/react/)

## ionic

onic是一个轻量的手机UI库，具有速度快，界面现代化、美观等特点。为了解决其他一些UI库在手机上运行缓慢的问题,
它直接放弃了IOS6和Android4.1以下的版本支持，来获取更好的使用体验 `摘自菜鸟教程`

ionic 特点 `摘自菜鸟教程`
1. ionic 基于Angular语法，简单易学。
2. ionic 是一个轻量级框架。
3. ionic 完美的融合下一代移动框架，支持 Angularjs 的特性， MVC ，代码易维护。
4. ionic 提供了漂亮的设计，通过 SASS 构建应用程序，它提供了很多 UI 组件来帮助开发者开发强大的应用。
5. ionic 专注原生，让你看不出混合应用和原生的区别
6. ionic 提供了强大的命令行工具。
7. ionic 性能优越，运行速度快。

### ionic 与 Native 的交互
ionic 对于原生的组件依赖与 cordova 插件
> Ionic Native is a TypeScript wrapper for Cordova/PhoneGap plugins that make adding any native functionality you need to your Ionic mobile app easy.
> Ionic Native wraps plugin callbacks in a Promise or an Observable, providing a common interface for all plugins and ensuring that native events trigger change detection in Angular.

使用 Ionic Native 必须包含其默认核心软件包 `ionic-native/core`
```
npm install @ionic-native/core --save
// 每个Ionic应用程序默认包含Ionic Native核心软件包
```

如要使用 Creame 功能,需要执行以下命令
```
//下载插件
npm install --save @ionic-native/camera
//安装插件
ionic cordova plugin add cordova-plugin-camera
```

### 需要了解
Angularjs、TypeScript
```
//创建
ionic start myApp
//运行
ionic serve
```


### 使用之后
* ionic 官网对于构建页面提供了一个编辑页面(拖拽式操作),注册后就能在 pc web 上使用,部分功能需要付费使用,编辑完成可以查看(下载),
* ionic 提供了自己的样式、组件库,在实际操作时可能满足不了需求,所以可以灵活的选择其他 样式库(MUI Framework7),也可以自己写.
* 同时 ionic 也是支持热更新的 [插件地址](https://github.com/nordnet/cordova-hot-code-push).
* 之前 ionic 有自己的编辑工具 Ionic Lab,但由于后期的维护成本、微软提供的Visual Studio中增加了Cordova和Ionic支持、提倡使用 CLI 等原因
被关闭(2016年底吧),可以参考这个文章 [Focusing on Fewer Things](https://blog.ionicframework.com/focusing-on-fewer-things/)

### 相关地址
[官网](https://ionicframework.com/) [国内的一个学习地址](http://www.ionic.wang/start-index.html)

## 补充
[框架的对比](https://www.zhihu.com/question/19558750/answer/91179040)  [阿里开源的Rax框架](https://www.zhihu.com/question/54738521)