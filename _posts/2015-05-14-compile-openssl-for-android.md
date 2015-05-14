---
layout: post
title:  "编译OpenSSL到 Android"
date:   2015-05-14 15:00:12
categories: Android
---

今天研究怎么把OpenSSL编译成Android原生库，记录一下。

## 1 安装NDK

首先，你得有NDK，[下载NDK](https://developer.android.com/tools/sdk/ndk/index.html)，然后将其解压到一个地方就可以了。我安装NDK的目录是：

{% highlight Bash shell scripts %}
/Volumes/DATA/develop/android-ndk-r10d
{% endhighlight %}

## 2 初始化Android交叉编译工具

使用NDK的功能初始化一个Android的GCC工具：

{% highlight Bash shell scripts %}
export NDK=/Volumes/DATA/develop/android-ndk-r10d
$NDK/build/tools/make-standalone-toolchain.sh --platform=android-19 --toolchain=arm-linux-androideabi-4.6 --install-dir=`pwd`/android-toolchain-arm
{% endhighlight %}

可以根据大家的需求选择Android版本号和GCC版本，我这里选择的是Android 4.4和GCC4.6。

然后大家会在 `android-toolchain-arm` 目录下找到生成的交叉编译器

![QQ20150514-1@2x](/uploads/2015/05/QQ20150514-1@2x.png)

## 3 获取OpenSSL

使用Git可以获取最新代码
{% highlight Bash shell scripts %}
$ git clone git://git.openssl.org/openssl.git
{% endhighlight %}

当然在Windows上要对git进行配置

{% highlight Bash shell scripts %}
$ cd openssl
$ git config core.autocrlf false
$ git config core.eol lf
$ git checkout .
{% endhighlight %}

## 4 设置环境变量

需要设置一下环境变量，使用刚刚设置好的交叉编译器，
其实也很简单，改一下 `PATH` 就可以了
{% highlight Bash shell scripts %}
$ export PATH=/Volumes/DATA/develop/android-ndk-r10d/android-toolchain-arm/arm-linux-androideabi/bin:$PATH
$ export ANDROID_DEV=/Volumes/DATA/develop/android-ndk-r10d/platforms/android-19/arch-arm/usr
{% endhighlight %}

记得把上面的地址改成你的 `android-toolchain-arm` 地址

## 5 编译

最后一步是编译了

{% highlight Bash shell scripts %}
$ ./Configure android-armv7
$ make -j
{% endhighlight %}

`-j` 选项的意思是，尽可能的使用多的线程进行编译。

这么编译出来的是静态库，也就是包含 `libssl.a` 和 `libcrypto.a`。
如果想编译成动态库，`./Configure` 的时候添加 `shared` 选项即可

其实编译很简单。


