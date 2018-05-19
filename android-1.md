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
