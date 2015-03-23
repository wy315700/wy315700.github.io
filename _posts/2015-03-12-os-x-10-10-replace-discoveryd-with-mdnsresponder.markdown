---
layout: post
title:  "OS X 10.10用原有的mDNSResponder替换bug百出的discoveryd"
date:   2015-03-12 20:56:12
categories: Mac
---
翻译自：

http://arstechnica.com/apple/2015/01/why-dns-in-os-x-10-10-is-broken-and-what-you-can-do-to-fix-it/

苹果在OSX 10.10里替换了DNS解析程序，把mDNSResponder替换成了discoveryd。

但是也许是因为是第一个版本吧，discoveryd漏洞实在太多，一个明显的表现是上网速度变慢。

mDNSResponder是用C编写的，并且是开源的。

而discoveryd是用C++编写的，并且暂时还没有开源。

其实我一直以为是我用的宽带的问题，本人用的移动宽带，没想到是系统的问题。

下面就来把discoveryd替换成mDNSResponder



### 首先你要有一个 OSX 10.9系统，将里面的文件提取出来

{% highlight Bash shell scripts linenos %}
cd ~/Desktop/
cp /usr/sbin/mDNSResponder .
cp /usr/sbin/mDNSResponderHelper .
cp /System/Library/LaunchDaemons/com.apple.mDNSResponder.plist .
cp /System/Library/LaunchDaemons/com.apple.mDNSResponderHelper.plist .
{% endhighlight %}


### 当然，如果你有 Time Machine备份也可以

{% highlight Bash shell scripts linenos %}
cd /Volumes/Time Machine Backups/Backups.backupdb/
cd <my machine name>
ls
cd <date/time of backup>
cd Macintosh\ HD
cp usr/sbin/mDNSResponder ~/Desktop/
cp usr/sbin/mDNSResponderHelper ~/Desktop/
cp System/Library/LaunchDaemons/com.apple.mDNSResponder.plist ~/Desktop/
cp System/Library/LaunchDaemons/com.apple.mDNSResponderHelper.plist ~/Desktop/
{% endhighlight %}

### 然后把提取出来的文件放到OSX 10.10中

{% highlight Bash shell scripts linenos %}
sudo cp mDNSResponder /usr/sbin/
sudo cp mDNSResponderHelper /usr/sbin/
sudo cp com.apple.mDNSResponder.plist /System/Library/LaunchDaemons/
sudo cp com.apple.mDNSResponderHelper.plist /System/Library/LaunchDaemons/
{% endhighlight %}

### 最后，修改系统使用mDNSResponder进行解析

{% highlight Bash shell scripts linenos %}
sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.discoveryd.plist
sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.discoveryd_helper.plist
sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.mDNSResponder.plist
sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.mDNSResponderHelper.plist
{% endhighlight %}

然后重启机器，大功告成，上网的时候再也不会卡在域名解析了。

当然，如果你要恢复使用discoveryd，也很简单

{% highlight Bash shell scripts linenos %}
sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.mDNSResponder.plist
sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.mDNSResponderHelper.plist
sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.discoveryd.plist
sudo launchctl load -w /System/Library/LaunchDaemons/com.apple.discoveryd_helper.plist
{% endhighlight %}


那么，如果你没有10.9的机器，也没有备份，怎么办呢。其实已经有大神打包好了一切

[https://gist.github.com/dlueth/36a3d5fbbc98a46ac14e](https://gist.github.com/dlueth/36a3d5fbbc98a46ac14e)
 

下载这个脚本，照做就行了