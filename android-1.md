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

## TextView 添加下划线

* 资源文件
```
<resources>
  <string name="hello"><u>Hello</u></string>
</resources>
```
* fromHtml
```
TextView textView = findViewById(R.id.tv_name); 
textView.setText(Html.fromHtml("<u>"+"hello"+"</u>"));
```

* java
```
tvTest.getPaint().setFlags(Paint. UNDERLINE_TEXT_FLAG );
tvTest.getPaint().setAntiAlias(true);
```

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
[官网连接](https://developer.android.com/topic/performance/graphics/load-bitmap#load-bitmap)

## 記一次 zip4j 解压

在解压(加密了)要获取解压的进度,zip4j 解压可以开启线程解压,解压进度是在这个类中控制 `ProgressMonitor`,同时包括 取消任务,
实践后结果,加密后解压耗时大于未加密后解压耗时,关于监听解压进度,网上有这么搞的：开启线程,里面死循环,延迟发送消息,当解压进度
为 100 时,作为跳出循环条件(Handler)
补充：
```
ProgressMonitor p = zipFile.getProgressMonitor();
long total = progressMonitor.getTotalWork(); //获得全部任务进度,在调用 extractAll 后设置的
int progress = progressMonitor.getPercentDone(); //获取当前进度
// 获取结果, 0-success,2-error,1-working,3-cancelled
int result = progressMonitor.getResult();
long workCompleted = progressMonitor.getWorkCompleted(); //当前任务值,内部用于计算进度
```

## 记一次AES加密

使用AES加密时密钥长度设置为 128位(一般情况下128位足矣)正常运行,但将密钥设置为 192、256位抛出 `java.security.InvalidKeyException: Illegal key size or default parameters ` 异常

原因：和 Java 的核心类库 JCE（Java Cryptography Extension）有关,JCE是一组包，它们提供用于加密、密钥生成和协商以及 Message Authentication Code（MAC）算法的框架和实现,默认模式下对密钥的长度是限制的

解决：
对于 JDK
1. 打开 [Java SE Downloads](http://www.oracle.com/technetwork/java/javase/downloads/index.html) ,找到 Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy Files for JDK/JRE 8 点击 DawnLoad,在下载页面下载
2. 解压文件得到 local_policy.jar、US_export_policy.jar,将 `Java\jdk1.8\jre\lib\security` 下的替换即可

对与 jre
1. 进入 Java\jre1.8.0_171\lib\security 目录,打开 `java.security` 文件
2. 找到 #crypto.policy=unlimited ,默认值是 limited, 放开注释 => crypto.policy=unlimited,即可

```
//生成密钥
public String getKey() {
	try {
		KeyGenerator kg = KeyGenerator.getInstance("AES");
		kg.init(192);
		SecretKey sk = kg.generateKey();
		byte[] b = sk.getEncoded();
		return Base64.getEncoder().encodeToString(b);
	} catch (NoSuchAlgorithmException e) {
		e.printStackTrace();
	}
	return "";
}
```

[参考链接](https://blog.csdn.net/dafeige8/article/details/75637058)

## Android 采集音频

Android 采集音频有两个类: AudioRecord MediaRecorder [AudioRecord vs MediaRecorder](https://stackoverflow.com/questions/5886872/android-audiorecord-vs-mediarecorder-for-recording-audio)
1. AudioRecord
可以处理原始音频流,对其进行实时处理,但不会自动压缩音频,Android6.0 对其有改进,输出的是 PCM 的语音数据,如果保存成音频文件是
不能被播放器播放的,要用到 AudioTrack 进行处理,基于字节流录音
> PCM: 脉冲编码调制,是存储音频最直接的机制

[AudioRecord API](https://developer.android.google.cn/reference/android/media/AudioRecord)

2. MediaRecorder
基于文件录音,封装度很高，操作简单,无法实现实时处理音频,输出的音频格式少,已集成了录音，编码，压缩等，支持少量的音频格式文件
(amr 3gp mpeg webm)
[支持文件格式](https://developer.android.google.cn/guide/topics/media/media-formats] [MediaRecorder API](https://developer.android.google.cn/reference/android/media/MediaRecorder)

一个工具类：[MediaUtils](https://github.com/Werb/MediaUtils)

## Glide 加载 Https 图片

针对版本 Glide v4.0
Glide 支持自定义配置的,所以设置加载 Https 需要在自定义组件里设置,如下：
```
@GlideModule
public class MyAppGlideModule extends AppGlideModule {
    @Override
    public void registerComponents(@NonNull Context context, @NonNull Glide glide, @NonNull Registry registry) {
        super.registerComponents(context, glide, registry);
        HttpsUtils.SSLParams sslParams = HttpsUtils.getSslSocketFactory(null, null, null);
        OkHttpClient okHttpClient = new OkHttpClient.Builder()
                .hostnameVerifier((hostname, session) -> {return true;})
                .sslSocketFactory(sslParams.sSLSocketFactory, sslParams.trustManager)
                .build();
        registry.replace(GlideUrl.class, InputStream.class, new OkHttpUrlLoader.Factory(okHttpClient));
    }
}
```
其中 `HttpsUtils` 是对 SSL 验证的工具类,
其中 `OkHttpUrlLoader` 需要自己实现,不过 Glide 提供了依赖(针对 OkHttp3): 
> implementation 'com.github.bumptech.glide:okhttp3-integration:4.7.1@aar'
> integration 下的代码就 4 个类,还是下载下来,不要依赖了

Glide 依赖:
> implementation 'com.github.bumptech.glide:glide:4.7.1'
> implementation 'com.github.bumptech.glide:annotations:4.7.1'
> annotationProcessor 'com.github.bumptech.glide:compiler:4.6.1'

[integration 下载地址](https://github.com/bumptech/glide/tree/master/integration/okhttp3/src/main/java/com/bumptech/glide/integration/okhttp3) 
[Glide 中文文档](https://muyangmin.github.io/glide-docs-cn/)

## webView 接收所有证书

继承 WebViewClient,重载 onReceivedSslError 方法
```
@Override
public void onReceivedSslError(WebView view, SslErrorHandler handler, SslError error) {
	handler.proceed();//接受所有的证书
}
```

## AIDL

* 创建 .aidl 文件
在 src/main 下新建 aidl 包(并且包结构和项目相同),自定义数据类型需要实现 Parcelable 接口,然后新建 .aidl 定义该类型.
除了基本数据类型，其他类型的参数都需要标上方向类型：in(输入), out(输出), inout(输入输出)
```
// IMyAidlInterface.aidl
package com.lemon.seeweibo.model.aidl;
import com.xx.xx.model.aidl.bean.UserBean;
interface IMyAidlInterface {
    void addUserBean(in UserBean userBean);
    UserBean getUserBean();
    String getString(String message);
}


// UserBean.aidl
package com.xx.xx.model.aidl.bean;
parcelable UserBean;
```

* Make Project 编译项目,会自动生成.aidl接口的实现
* 写服务端 Service 并注册
```
public class MyAidlService extends Service {
    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        return iBinder;
    }
    /**
     * 创建本地 Binder 对象
     */
    private IMyAidlInterface.Stub iBinder = new IMyAidlInterface.Stub() {
        @Override
        public void addUserBean(UserBean userBean) throws RemoteException {
        }
        @Override
        public UserBean getUserBean() throws RemoteException {
            return null;
        @Override
        public String getString(String message) throws RemoteException {
            return null;
        }
    };
}

<service
	android:name=".model.aidl.MyAidlService"
	android:enabled="true"
	android:exported="true"/>
```

* 写客户端 ServiceConnection

客户端需要将服务端的 .aidl 拷贝过来(包括自定义实体类)

```
private IMyAidlInterface iMyAidlInterface;
private ServiceConnection mConnection = new ServiceConnection {
    private IMyAidlInterface iMyAidlInterface;
    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
        iMyAidlInterface = IMyAidlInterface.Stub.asInterface(service);
    }

    @Override
    public void onServiceDisconnected(ComponentName name) {
        iMyAidlInterface = null;
    }
    public IMyAidlInterface getiMyAidlInterface() {
        return iMyAidlInterface;
    }
}
```

* 客户端绑定服务

```
String servePackageName = ""; //服务端包名
String serveServiceName = ""; //服务Service名
ComponentName componentName = new ComponentName( servePackageName ,serveServiceName);
Intent intent = new Intent();
intent.setComponent(componentName);
bindService(intent,serviceConnection, Context.BIND_AUTO_CREATE);
```

* 开始通讯

```
iMyAidlInterface.addUserBean(new UserBean("Lee","1278",23));
iMyAidlInterface.getUserBean();
```
[Android 接口定义语言 (AIDL)](https://developer.android.google.cn/guide/components/aidl)

## 有关内存管理

ActivityManager.getMemoryClass() 可返回当前应用的可用内存大小(单位M)
OnLowMemory 在系统内存不足时,会杀死所有程序(按照优先级),然后会调用该方法
OnTrimMemory(int level) 是Android 4.0 之后提供的方法,每次执行优先级计算时都会回调该方法,它返回的状态有：

* TRIM_MEMORY_COMPLETE 该进程在后台进程列表(内存缓存列表)的最后一个,有随时被回收的可能,在这里应该释放一些资源,保证其不被杀死
* TRIM_MEMORY_MODERATE 该进程在后台进程列表(内存缓存列表)的中间位置
* TRIM_MEMORY_BACKGROUND 此时系统内存已经较低了,该进程在后台进程(内存缓存列表)最近的位置,目前不会被回收
* TRIM_MEMORY_UI_HIDDEN 应用从前台切换到后台运行,回收UI资源
* TRIM_MEMORY_RUNNING_CRITICAL 该进程处于前台运行,系统已经回收了大部分进程
* TRIM_MEMORY_RUNNING_LOW 该进程处于前台运行,但系统内存已经非常低了
* TRIM_MEMORY_RUNNING_MODERATE 该进程处于前台运行,但系统内存有点低

OnLowMemory和OnTrimMemory的比较：

1. OnLowMemory被回调时，已经没有后台进程；而onTrimMemory被回调时，还有后台进程。
2. OnLowMemory是在最后一个后台进程被杀时调用，一般情况是low memory killer 杀进程后触发,而OnTrimMemory的触发更频繁,
每次计算进程优先级时，只要满足条件，都会触发。
3. 通过一键清理后，OnLowMemory不会被触发，而OnTrimMemory会被触发一次。

一些内存优化的辅助工具：
MAT、Heap Viewer、Allocation Tracker、Memory Monitor、LeakCanary

## 线程

* Thread.sleep() 阻塞当前线程,不会释放锁
* Object.wait() 让出 CPU，释放对象锁
* Thread.yield() 暂停当前线程,让出CPU给同等优先级的或更高优先级的处于就绪状态的线程,它不会阻塞线程,而是在调用后该线程立即进入就绪状态
不会释放锁
* Thread.join() 当前线程进入阻塞状态,需要等待其他线程执行完成才会执行
* suspend() 和 resume() :suspend()使得线程进入阻塞状态,并且不会自动恢复,只有智执行resume(),才能使得线程重新进入可执行状态
* interrupt() 中断线程
* interrupted() 当前线程是否已经中断(返回 boolean)
* isAlive() 线程是否处于活动状态
* setPriority() 设置线程优先级(1~10)
* Object.notify() 随机唤醒某一个线程
* Object.notifyAll() 唤醒所有线程

## 多线程中的锁

悲观锁：假定会发生并发冲突,所以每次拿到数据都要加锁,synchronized 就是一种悲观锁,它会使其他线程挂起,等待当前线程释放
锁,传统的关系型数据库里有很多这种锁机制(读锁、写锁、表锁),可能产生死锁

乐观锁：假设拿数据时不会发生并发冲突,所以不会加锁,如果冲突失败就会重试,植到成功，它使用的机制是 CAS(Compare and Swap),但 CAS 虽然高效解决了
原子操作,但仍然存在问题:

1. ABA 问题,如果一个值原来是 A,变成了 B, 又变成了 A,那么 CAS 会检查该值并没有改变,但实际改变了,解决办法就是加版本号,每次更新的时候版本加1：
A1-B2-A3,java 里 atomic 包里提供了 AtomicStampedReference 来解决 ABA 问题,它是通过检查当前引用是否等于预期引用来判断的
2. 循环时间开销时间大,自旋 CAS 如果长时间不成功,CPU 会带来很大开销
3. 只能保证一个共享变量的原子操作,当对一个共享变量执行操作时,可以使用 循环 CAS 方式保证原子操作;但当对多个共享变量操作时,循环操作失去
作用,这时需要使用锁机制 

Java虚拟机对synchronized的优化：偏向锁、轻量级锁、自旋锁

自旋锁：让当前线程不停地的在循环体内执行实现的，当循环的条件被其他线程改变时 才能进入临界区

公平锁：加锁前先查看是否有排队等待的线程，有的话优先处理排在前面的线程，先来先得。
非公平锁：线程加锁时直接尝试获取锁，获取不到就自动到队尾等待，非公平锁会有更多的机会去抢占锁
Java 中通过 ReentrantLock 来实现,大部分场景中使用的是非公平锁,非公平锁性能高于公平锁性能,因为公平锁需要在多核情况下维护一个队列,
如果当前线程不是队列的第一个,无法获取锁,增加了线程切换次数.
```
//创建一个非公平锁，默认是非公平锁
Lock nonFairLock= new ReentrantLock();
Lock nonFairLock= new ReentrantLock(false);

//创建一个公平锁，构造传参true
Lock fairLock= new ReentrantLock(true);
```

## APP 启动方式

* 冷启动: app 第一次启动,当前app进程不存在,系统需要创建进程、初始化(先创建和初始化Application,在创建 activity)
* 温启动：当 Activity 因为内存不足时被回收，这时不需要重新创建进程,需要重新加载 Activity
* 热启动：app 进程存在且 Activity 对象仍然在内存中没有被回收,这样启动就避免了对象初始化、布局重绘(不会创建和初始化Application)

测量应用启动时间：

1. adb shell am start -W [packageName]/[packageName.launchActivity]

```
adb shell am start -W com.git.test/.testActivity
Starting:xxx                                                            
Status:ok 
Activity: com.git.test/.testActivity 
ThisTime:2871
TotalTime:2871（应用的启动时间）
WaitTime: 2949
Complete
```

2. 通过 Log 过滤 ActivityManager和Display

启动优化：

* 减少打开的 LaunchActivity 的 view 层级,减少 view 的绘制时间
* 主线程不要进行耗时操作
* Application 中初始化第三方的SDK,做成懒加载
* app 启动的黑屏/白屏,出现原因：启动app时,系统会加载app主题中的windowBackground作为app的预览元素,当app启动时间太长,
就会出现这种情况(黑屏白屏取决与主题),解决可以通过设置透明主题：
```
<style name="Theme.Splash" parent="Theme.AppCompat.Light.NoActionBar">
	<item name="windowActionBar">false</item>
	<item name="windowNoTitle">true</item>
	<item name="android:windowFullscreen">true</item>
	<item name="android:windowBackground">@drawable/p_login_bg</item> //背景图
</style>

<activity
	android:name=".ui.SplashActivity"
	android:theme="@style/Theme.Splash">
	<intent-filter>
		<action android:name="android.intent.action.MAIN" />
		<category android:name="android.intent.category.LAUNCHER" />
	</intent-filter>
</activity>
```
* 在 application 中开启 IntentService,来进行耗时操作(未验证)
```
public class MyApplication extends Application{
    @Override
    public void onCreate() {
        super.onCreate();
        InitializeService.start(this);
    }
}


public class InitializeService extends IntentService{

    private static final String ACTION = "TEST";

    public InitializeService(String name) {
        super("InitializeService");
    }

    public static void start(Context context){
        Intent intent = new Intent(context,InitializeService.class);
        intent.setAction(ACTION);
        context.startService(intent);
    }

    @Override
    protected void onHandleIntent(@Nullable Intent intent) {
        if(intent!=null){
			//初始化第三方 sdk
        }
    }
}
```

## 关于视频播放的屏幕旋转问题

正常情况下的 activity 生命周期：onCreate -> onStart -> onRestoreInstanceState -> onResume -> onPause ->
 onSaveInstanceState -> onStop -> onDestory

1. 设置：android:configChanges="orientation", 生命周期会重新走一遍
2. 设置：android:configChanges="orientation|screenSize" 或 android:configChanges="orientation|screenSize|keyboardHidden"
生命周期：onCreate -> onStart -> onRestoreInstanceState -> onResume
切换方向： onConfigurationChanged

强制设置屏幕方向： android:screenOrientation="landscape|portrait|sensor",代码控制：
setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE)

视频播放屏幕切换的解决方案：

1. 系统自己决定,设置 android:configChanges="orientation|screenSize|keyboardHidden"
建立 res/layout-land(横屏布局) res/layout-port(竖屏布局) res/layout(默认布局)
缺点： 生命周期重走,需要保存播放进度,然后调用 seekTo
2. 设置 android:configChanges="orientation|screenSize|keyboardHidden" android:screenOrientation="sensor"
在 onConfigurationChanged 方法中 重新查找控件进行隐藏等操作,可以不需要创建 res/layout-land 等文件夹

## 一些方法

getExternalFilesDir(Environment.DIRECTORY_PICTURES) 获得系统相册路径
DIRECTORY_DOWNLOADS 下载的标准目录
DIRECTORY_DCIM 相机拍摄照片和视频的标准目录

禁止多媒体库扫描指定文件夹下的多媒体文件:在需要不被扫描的文件夹下新建一个隐藏文件,文件名为”.nomedia”即可










