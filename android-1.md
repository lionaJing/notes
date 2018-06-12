### Android 获取文字的高(宽)度

1. 获取真实宽度、高度
```
Rect rect = new Rect();
paint.getTextBounds(text,0,text.length(), rect);
width = rect.width()
height = rect.height()
```
2. 通过画笔获取宽度
```
width = paint.measureText(text,0,text.length());
```
[链接](https://blog.csdn.net/u010661782/article/details/52805939)

### Intent 属性

1. setAction
代表了系统中已经定义了一系列常用的动作,使用时通过 `setAction()`或在清单文件中
设置(注意：只要设置setAction ()就要在清单文件AndroidManifest.xml中设置action属性)
```
<activity android:name=".MainActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN"/>
        <action android:name="android.intent.action.DIAL"/>
        <category android:name="android.intent.category.LAUNCHER"/>
    </intent-filter>
</activity>
```
[官方 Action 列表](https://developer.android.google.cn/reference/android/content/Intent#constants_1)

2. setData
设置 Intent 需要操作的数据(Uri 形式封装),此方法会自动清除 `setType()、setTypeAndNormalize()`设置的类型，
注意 Uri 中的 String 需要是小写的,还可以在清单文件中指定 data,在清单文件中指定就是让 Intent 对意图进行
过滤
```
<activity android:name=".MainActivity"> 
	<intent-filter>
		<action android:name="com.scott.intent.action.MAIN"/>
		<category android:name="android.intent.category.DEFAULT"/>
		<data android:scheme="learn" android:host="com.lemon" android:port="8081" android:path="/intent"/>
	</intent-filter>
</activity>

//代码调用
Intent intent = new Intent();
Uri uri =  Uri.parse("learn://com.lemon:8081/intent");
intent.setData(uri); 
```

3. setType
指定返回的数据类型(参数需要为小写,且要符合“abc/xyz”这样的字符串格式),与 `setData` 冲突,不可同时使用

4. Category
设置当前 Action 执行的环境，可以通过 `addCategory()`或在清单文件中指定
[官方 Category 列表](https://developer.android.google.cn/reference/android/content/Intent#category_alternative)
> CATEGORY_LAUNCHER 应用的入口(需要与 ACTION_MAIN 同时设置)
> CATEGORY_BROWSABLE 能被浏览器安全调用
> CATEGORY_HOME 显示当前应用的主界面
> CATEGORY_APP_BROSER：打开浏览器应用
> CATEGORY_APP_MAPS：打开计地图应用
> CATEGORY_APP_CALCLATOR：打开计算器应用
> CATEGORY_APP_EMAIL：打开email应用
> CATEGORY_APP_GALLERY：打开画廊应用

### 打开照相机


### DI

依赖注入（Dependency Injection，简称 DI）是用于实现控制反转（Inversion of Control，缩写为 IoC）最常见的方式之一，
控制反转是面向对象编程中的一种设计原则，用以降低计算机代码之间耦合度。控制反转的基本思想是：借助“第三方”实现具有
依赖关系的对象之间的解耦。一开始是对象 A 对 对象 B 有个依赖，对象 A 主动地创建 对象 B，对象 A 有主动控制权，实现
了 Ioc 后，对象 A 依赖于 Ioc 容器，对象 A 被动地接受容器提供的对象 B 实例，由主动变为被动，因此称为控制反转。
注意，控制反转不等同于依赖注入