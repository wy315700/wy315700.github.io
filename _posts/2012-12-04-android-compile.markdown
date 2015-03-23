---
layout: post
title:  "Android 编译步骤"
date:   2012-12-04 09:56:12
categories: Android
---
基于CENTOS 6.2

<code style="color: #dd1144;">首先，一定是要64位系统，内存要在2G以上。</code>

## 1. 安装git ：

git 在官方源里是没有的，需要安装epel和rpmforge源。

从 http://fedoraproject.org/wiki/EPEL/zh-cn 下载
[http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm](http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm){:target="_blank"} 并安装
 
从 http://pkgs.repoforge.org/rpmforge-release/ 下载
[http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.2-2.el6.rf.x86_64.rpm](http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.2-2.el6.rf.x86_64.rpm){:target="_blank"}并安装
找对应的rpm包安装好
 
然后安装git

{% highlight Bash shell scripts %}
yum install git -y
{% endhighlight %}

## 2.获取源代码：

{% highlight Bash shell scripts %}
$ mkdir ~/bin 
$ PATH=~/bin:$PATH //最好写到~.bashrc里 
{% endhighlight %}

这个目录是放 repo脚本的，repo脚本是对git的一种封装。详情见：
http://source.android.com/source/version-control.html
获取脚本

{% highlight Bash shell scripts %}
$ curl https://dl-ssl.google.com/dl/googlesource/git-repo/repo > ~/bin/repo 
$ chmod a+x ~/bin/repo
{% endhighlight %}

然后回到主目录后 建立存放源代码的目录

{% highlight Bash shell scripts %}
$ mkdir WORKING_DIRECTORY 
$ cd WORKING_DIRECTORY
{% endhighlight %}

然后执行repo

{% highlight Bash shell scripts %}
$ repo init -u https://android.googlesource.com/platform/manifest 
{% endhighlight %}

如果要获取除master外的分支，用下面这条命令，在后面加 -b 然后跟分支版本。
{% highlight Bash shell scripts %}
$ repo init -u https://android.googlesource.com/platform/manifest -b android-4.0.1_r1
{% endhighlight %}

最后就可以获取代码了。

{% highlight Bash shell scripts %}
$ repo sync
{% endhighlight %}

源代码很大大概有7G。而且由于git有本地分支缓存，所以最后是占了14G的空间。

## 3.组建编译平台。
 
网上的教程都是基于ubuntu的，我在centos下组建。

{% highlight Bash shell scripts %}
sudo apt-get install git-core gnupg flex bison gperf build-essential  zip curl zlib1g-dev libc6-dev lib32ncurses5-dev ia32-libs  x11proto-core-dev libx11-dev lib32readline5-dev lib32z-dev  libgl1-mesa-dev g++-multilib mingw32 tofrodos python-markdown  libxml2-utils xsltproc
{% endhighlight %}

这是谷歌给的官方的基于ubuntu 10.04的脚本
 
基于centos的话，

{% highlight Bash shell scripts %}
yum install flex bison* gperf make gcc gcc-c++ kernel-devel zlib zlib-devel zlib.i686 zlib-devel .i686 glibc* glibc*.i686 ncurses* ncurses*.i686 libX11 libX11-devel libXext libX11.i686 libX11-devel.i686 libXext.i686 readline readline.i686 mesa-libGL* mesa-libGL*.i686 unix2dos dos2unix python-markdown* libxslt* libxslt*.i686 libtool* libtool*.i686 readline* readline*.i686 -y
{% endhighlight %}

9.25 添加以下库：yum install alsa* alsa*.i686 wx* wx*.i686 -y // 这个是编译emulator要用的 2.2.3的emulator和system.img编译是分开的
 
 
下载JDK 不能用内置的Open jdk ，要自己去下载sunjdk 而且版本是要1.6的
http://www.oracle.com/technetwork/java/javase/downloads/jdk-6u26-download-400750.html
 
编译2.2.3的话还要修改main.mk 把里面的JAVA版本1.5改成1.6
 
还可以设置缓存，当经常更换编译分支的时候可以用缓存进行加速：
 
在.bashrc里添加以下命令：

{% highlight Bash shell scripts %}
export USE_CCACHE=1
{% endhighlight %}

默认情况下缓存会被放到~/.ccache目录下，如果需要更换目录，输入以下命令：

{% highlight Bash shell scripts %}
export CCACHE_DIR=
{% endhighlight %}

这条命令是设置缓存大小的

{% highlight Bash shell scripts %}
prebuilt/linux-x86/ccache/ccache -M 50G
{% endhighlight %}

默认的输出文件会在 out/ 目录下
如果需要更换：

{% highlight Bash shell scripts %}
export OUT_DIR_COMMON_BASE=
{% endhighlight %}

接下来就是导入key

{% highlight Bash shell scripts %}
$ gpg --import
-----BEGIN PGP PUBLIC KEY BLOCK----- Version: GnuPG v1.4.2.2 (GNU/Linux) mQGiBEnnWD4RBACt9/h4v9xnnGDou13y3dvOx6/t43LPPIxeJ8eX9WB+8LLuROSV lFhpHawsVAcFlmi7f7jdSRF+OvtZL9ShPKdLfwBJMNkU66/TZmPewS4m782ndtw7 8tR1cXb197Ob8kOfQB3A9yk2XZ4ei4ZC3i6wVdqHLRxABdncwu5hOF9KXwCgkxMD u4PVgChaAJzTYJ1EG+UYBIUEAJmfearb0qRAN7dEoff0FeXsEaUA6U90sEoVks0Z wNj96SA8BL+a1OoEUUfpMhiHyLuQSftxisJxTh+2QclzDviDyaTrkANjdYY7p2cq /HMdOY7LJlHaqtXmZxXjjtw5Uc2QG8UY8aziU3IE9nTjSwCXeJnuyvoizl9/I1S5 jU5SA/9WwIps4SC84ielIXiGWEqq6i6/sk4I9q1YemZF2XVVKnmI1F4iCMtNKsR4 MGSa1gA8s4iQbsKNWPgp7M3a51JCVCu6l/8zTpA+uUGapw4tWCp4o0dpIvDPBEa9 b/aF/ygcR8mh5hgUfpF9IpXdknOsbKCvM9lSSfRciETykZc4wrRCVGhlIEFuZHJv aWQgT3BlbiBTb3VyY2UgUHJvamVjdCA8aW5pdGlhbC1jb250cmlidXRpb25AYW5k cm9pZC5jb20+iGAEExECACAFAknnWD4CGwMGCwkIBwMCBBUCCAMEFgIDAQIeAQIX gAAKCRDorT+BmrEOeNr+AJ42Xy6tEW7r3KzrJxnRX8mij9z8tgCdFfQYiHpYngkI 2t09Ed+9Bm4gmEO5Ag0ESedYRBAIAKVW1JcMBWvV/0Bo9WiByJ9WJ5swMN36/vAl QN4mWRhfzDOk/Rosdb0csAO/l8Kz0gKQPOfObtyYjvI8JMC3rmi+LIvSUT9806Up hisyEmmHv6U8gUb/xHLIanXGxwhYzjgeuAXVCsv+EvoPIHbY4L/KvP5x+oCJIDbk C2b1TvVk9PryzmE4BPIQL/NtgR1oLWm/uWR9zRUFtBnE411aMAN3qnAHBBMZzKMX LWBGWE0znfRrnczI5p49i2YZJAjyX1P2WzmScK49CV82dzLo71MnrF6fj+Udtb5+ OgTg7Cow+8PRaTkJEW5Y2JIZpnRUq0CYxAmHYX79EMKHDSThf/8AAwUIAJPWsB/M pK+KMs/s3r6nJrnYLTfdZhtmQXimpoDMJg1zxmL8UfNUKiQZ6esoAWtDgpqt7Y7s KZ8laHRARonte394hidZzM5nb6hQvpPjt2OlPRsyqVxw4c/KsjADtAuKW9/d8phb N8bTyOJo856qg4oOEzKG9eeF7oaZTYBy33BTL0408sEBxiMior6b8LrZrAhkqDjA vUXRwm/fFKgpsOysxC6xi553CxBUCH2omNV6Ka1LNMwzSp9ILz8jEGqmUtkBszwo G1S8fXgE0Lq3cdDM/GJ4QXP/p6LiwNF99faDMTV3+2SAOGvytOX6KjKVzKOSsfJQ hN0DlsIw8hqJc0WISQQYEQIACQUCSedYRAIbDAAKCRDorT+BmrEOeCUOAJ9qmR0l EXzeoxcdoafxqf6gZlJZlACgkWF7wi2YLW3Oa+jv2QSTlrx4KLM= =Wi5D -----END PGP PUBLIC KEY BLOCK-----
{% endhighlight %}

最后还要输入EOF
接下来就可以验证了

{% highlight Bash shell scripts %}
$ git tag -v TAG_NAME
{% endhighlight %}

## 4.编译：
 
先设置环境

{% highlight Bash shell scripts %}
$ source build/envsetup.sh
{% endhighlight %}

然后选一个目标

{% highlight Bash shell scripts %}
$ lunch full-eng 
{% endhighlight %}

各个编译目标的解释在 http://source.android.com/source/building.html#choose-a-target
直接输入 lunch以后有选项可以选择的
 
 
接下来就可以编译了

{% highlight Bash shell scripts %}
make -j4
{% endhighlight %}

其中j后面跟着的数字表示用多少个线程进行编译
编译的时候真的需要耐心，因为，，，真的很慢
很慢
 
中途爆了一个错误：
host Executable: acp (out/host/linux-x86/obj/EXECUTABLES/acp_intermediates/acp)
host Executable: aapt (out/host/linux-x86/obj/EXECUTABLES/aapt_intermediates/aapt)
/usr/bin/ld: skipping incompatible /usr/lib64/libz.so when searching for -lz
/usr/bin/ld: cannot find -lz

 

输入这两条命令：

cd  /usr/lib/
ln -s ../../lib/libz.so.1.2.3 libz.so

编译了一天一夜，机器卡到爆为止了。第一次编译会很慢，以后几次就快了。
 
最后编译完成，
在out/target/product/generic/下可以找到system.img和system文件夹 

## 5.运行

{% highlight Bash shell scripts %}
$ emulator
{% endhighlight %}

每次运行的时候都要先运行：

{% highlight Bash shell scripts %}
$ source build/envsetup.sh
$ lunch full-eng
{% endhighlight %}

这样就可以了

## 6.单独编译组件
安卓允许单独编译组件，只要存在Android.mk文件的文件夹都允许单独编译。

{% highlight Bash shell scripts %}
$ source build/envsetup.sh 
{% endhighlight %}

执行完这条命令以后就会多出来几条命令，其中mm和mmm是用来单独编译组件的，

mm是编译指定目录。

mmm是编译当前目录。

然后编译完了回到原代码的主目录以后用

{% highlight Bash shell scripts %}
make snod
{% endhighlight %}

重新生成system.img文件。
这条命令的原理是把system文件夹重新打包成system.img文件

## 7.编译sdk

从安卓的源代码里还能编译出sdk。
直接执行make 是不包括sdk的

{% highlight Bash shell scripts %}
make PRODUCT-sdk-sdk
{% endhighlight %}

这样就可以了
编译出来的SDK在out/host/linux-x86/sdk下
不过需要注意的是，每次完整编译或者编译单独模块的时候，都会把SDK编译的结果清空，所以最好编译完了备份一下。
