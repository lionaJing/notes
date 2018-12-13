# Android with NDK dev
------

NDK(Native Development Kit),快速开发C（或C++）的动态库，并能自动将so和java应用一起打包成apk.
作用：
1. 运行效率高(直接调用的是本地代码)
2. 安全(java代码容易被反编译,C/C++被反编译难度较大)
3. 便于移植.扩展性高
4. 交叉编译,可以生成特定平台的动态库
(计算密集型、算法效率要求高的、包含敏感信息的等会考虑使用 NDK 开发)

Androdi 进行 NDK 开发有两种方式

> ndk-build + Android.mk + Application.mk(Androdi Studio2.2 之前)
> CMake + CMakeLists.txt(Android Studio 之后)

新建一个 project(support C/C++),里面的 CMakeLists.txt 定义了三个方法:
1. add_library(param1,param2,param3)
param1: 指定生成共享库的名字(so 库的名字,注意在代码中加载 so 库时不能加后缀),
param2: 用于指定生成的是动态库 or 静态库取值(STATIC、SHARED)

> 静态库:以 .a结尾。静态库在程序链接的时候使用,链接器会将程序中使用到函数的代码从库文件中拷贝到应用程序中。
> 一旦链接完成,在执行程序的时候就不需要静态库了。 
> 共享库:以 .so结尾。在程序的链接时候并不像静态库那样在拷贝使用函数的代码,而只是作些标记。然后在程序开始启
> 动运行的时候,动态地加载所需模块。
 
param3: 指定需要编译的源文件(相对路径,支撑多个文件)

```
add_library(native-lib,SHARED,src/main/cpp/native-lib.cpp src/main/cpp/other.cpp)

System.loadLibrary(“native-lib”); 
```

2. find_library(param1,param2)
param1: 一个变量或指针
param2: NDK 中对应的库的名字
该方法用于将 NDK 中的库与定义的变量向关联

3.target_link_libraries(param1,param2)
param1: 自己定义的共享库
param2: 第三方库 or 系统 NDK 库
将第三方库(或系统库)与我们的库 link

### C 代码名规则

* C/C++ 代码名为 xxx，则生成的 so 库名为 ：libxxx.so
* 引用 so 库时，不能添加 lib 和 后缀.so(引用 xxx)
* C/C++ 代码内方法名格式为 ：Java_包名类名_方法名
> 包名：com.github.myndk
> 最终方法名：Java_com_github_myndkNativeTest_callString

### .h 头文件中的JNIEnv *env

在 Android 中 Java 代码被编译成 dex 文件,有虚拟机解释成机器语言在去执行，
C/C++ 代码通过 NDK 打包成 so 文件，然后与 java 代码进行通讯，而 env 就是通讯的桥梁
JavaVM : JavaVM 是 Java虚拟机在 JNI 层的代表, JNI 全局仅仅有一个;
JNIEnv : JavaVM 在线程中的代表,每一个线程都有一个,JNI 中可能有非常多个 JNIEnv;
是一个指向全部JNI方法的指针,该指针只在创建它的线程有效,不能跨线程传递,因此不同线程的
JNIEnv是彼此独立的.
(C++环境下, JNIEnv 是结构体,C语言环境下, JNIEnv是指针)

### JNIEnv的主要作用有两点：

* 调用 Java 函数(使用反射) : JNIEnv 代表 Java 执行环境, 能够使用 JNIEnv 调用 Java 中的代码;
* 操作 Java 对象 : Java 对象传入 JNI 层就是 Jobject 对象,须要使用 JNIEnv 来操作这个 Java 对象;

### JNIEnv是什么时候产生

当Android中第一个Java线程要调用本地的C/C++代码的时候。DVM就会为该线程产生一个JNIEnv*的指针。
而每个线程在和C/C++互相调用的时候。其相应的JNIEnv 也是相互独立

### 编译出多个 so

使用命令 add_subdirectory(),参数为 CMakeLists.txt 所在的相对路径

### NDK 保存密钥
```
#include <jni.h>
JNIEXPORT jstring JNICALL Java_com_github_myndkNativeTest_getKey(JNIEnv *env, jclass type) {
	char* key = "";
	return (*env)->NewStringUTF(env, key);
}
// JNIEXPORT 标志
// jstring 返回值
// JNICALL Jni调用XXX(后面的XXX就是JAVA的方法名)

//调用
public class NativeHelper {
	static {
		System.loadLibrary("native-lib");
	}
	public static native String getKey();
}

String appKey = NativeHelper.getKey();
```
jclass 与 jobject 不同：
jclass: 是一个类操作，因为它从一个类中获得field的描述
jobject: 这是一个实例操作，因为它从这个实例中获得这个field的值

### armeabi armeabi-v7a
armeabi、armeabi-v7a、mips、x86 都是cpu的类型
mips / mips64: 极少用于手机可以忽略
x86 / x86_64: x86是可以兼容armeabi平台运行的,打包出的x86的so,总会比armeabi平台的体积更小,可以忽略?
armeabi:  针对普通的或旧的arm v5 cpu,通用性很强。但是速度慢
armeabi-v7a:是针对有浮点运算或高级扩展功能的arm v7 cpu(目前主流版本)

同时包含了 armeabi， armeabi-v7a和x86 所有设备都可以运行,同时也会导致包变大

### 问题

提示 "mips64el-linux-android-strip" 错误
原因: NDK r17 之后不再支持 mips 平台
解决：build.gradle 增加配置
```
android {

	defaultConfig {
		.....
	}

	packagingOptions { 
		doNotStrip '*/mips/*.so' 
		doNotStrip '*/mips64/*.so' 
	}
}
```

### 链接
[NDK 知识梳理(2) CMakeLists.txt ](https://www.aliyun.com/jiaocheng/15077.html?spm=5176.100033.2.6.uX6Ffh )
[CMakeLists解析](https://www.cnblogs.com/chenxibobo/p/7678389.html )
[官方 Demo](https://github.com/googlesamples/android-ndk/tree/master/hello-libs )
[JNI 常用函数](https://www.cnblogs.com/Free-Thinker/p/6168945.html )
[NDK 开发1](https://www.jianshu.com/p/6332418b12b1 )
[NDK 开发2](https://www.jianshu.com/p/0261e6cceb3e )
