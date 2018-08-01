## Android 获取文字的高(宽)度

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

## Intent 属性

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

## 打开照相机


## DI

依赖注入（Dependency Injection，简称 DI）是用于实现控制反转（Inversion of Control，缩写为 IoC）最常见的方式之一，
控制反转是面向对象编程中的一种设计原则，用以降低计算机代码之间耦合度。控制反转的基本思想是：借助“第三方”实现具有
依赖关系的对象之间的解耦。一开始是对象 A 对 对象 B 有个依赖，对象 A 主动地创建 对象 B，对象 A 有主动控制权，实现
了 Ioc 后，对象 A 依赖于 Ioc 容器，对象 A 被动地接受容器提供的对象 B 实例，由主动变为被动，因此称为控制反转。
注意，控制反转不等同于依赖注入

## Drawable

ContextCompat.getDrawable(context,R.mipmap.ic)

## BitmapFactory

BitmapFactory 解码一个较大文件,会触发内存溢出,BitmapFactory.Options用于解码Bitmap时对各种参数的控制：
* inJustDecodeBounds 解码的时不会返回bitmap,只会返回bitmap的尺寸,不会加载到内存中
* inSampleSize 控制图片的缩放, == 1时图片原始大小, > 1时,比如 2,则采样后的图片其宽/高均为原图大小的1/2
而像素数为原图的1/4，其占用内存大小也为原图的1/4
* inPurgeable ture表示创建的bitmap在内存不足的情况下可以被回收,如果再次调用,则重新decode和inInputShareable一起使用,
* inInputShareable false忽略,true 设置bitmap是否可以共享数据比如inputstream，array等
* inPreferredConfig 指定编码格式
* inDither 设置是否抖动处理

Bitmap二次采样的流程：
1. 获取图片的边框的长度与高度
```
BitmapFactory.Options options = new BitmapFactory.Options();
options.inJustDecodeBounds = true;
BitmapFactory.decodeResource(getResources(), R.id.myimage, options);
int imageHeight = options.outHeight;
int imageWidth = options.outWidth;
String imageType = options.outMimeType;
```
2. 计算出采样率inSampleSize
```
public static int calculateInSampleSize(
            BitmapFactory.Options options, int reqWidth, int reqHeight) {
    // Raw height and width of image
    final int height = options.outHeight;
    final int width = options.outWidth;
    int inSampleSize = 1;

    if (height > reqHeight || width > reqWidth) {

        final int halfHeight = height / 2;
        final int halfWidth = width / 2;

        // Calculate the largest inSampleSize value that is a power of 2 and keeps both
        // height and width larger than the requested height and width.
        while ((halfHeight / inSampleSize) >= reqHeight
                && (halfWidth / inSampleSize) >= reqWidth) {
            inSampleSize *= 2;
        }
    }

    return inSampleSize;
}
```
3. 重新加载图片
```
public static Bitmap decodeSampledBitmapFromResource(Resources res, int resId,
        int reqWidth, int reqHeight) {

    // First decode with inJustDecodeBounds=true to check dimensions
    final BitmapFactory.Options options = new BitmapFactory.Options();
    options.inJustDecodeBounds = true;
    BitmapFactory.decodeResource(res, resId, options);

    // Calculate inSampleSize
    options.inSampleSize = calculateInSampleSize(options, reqWidth, reqHeight);

    // Decode bitmap with inSampleSize set
    options.inJustDecodeBounds = false;
    return BitmapFactory.decodeResource(res, resId, options);
}
```

最终调用： mImageView.setImageBitmap(
    decodeSampledBitmapFromResource(getResources(), R.id.myimage, 100, 100));
(官网连接)[https://developer.android.com/topic/performance/graphics/load-bitmap#load-bitmap]

## 一些方法

getExternalFilesDir(Environment.DIRECTORY_PICTURES) 获得系统相册路径
DIRECTORY_DOWNLOADS 下载的标准目录
DIRECTORY_DCIM 相机拍摄照片和视频的标准目录

禁止多媒体库扫描指定文件夹下的多媒体文件:在需要不被扫描的文件夹下新建一个隐藏文件,文件名为”.nomedia”即可










