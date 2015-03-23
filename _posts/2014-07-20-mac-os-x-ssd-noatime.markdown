---
layout: post
title:  "Mac OS X 设置noatime标志"
date:   2014-07-20 10:56:12
categories: mac
---
自从给博主的MAC Mini 配上了SSD，也是各种宝贵啊，生怕哪一天可爱的SSD就罢工不干了，前段时间刚研究出 [如何在10.9.4上打开trim]({% post_url 2014-07-02-mac-osx-10-9-4-trim %})。

这几天又在眼睛别的优化了。而Mac 作为一个类Unix的系统，默认情况下每次访问文件的时候都会更新文件的最后访问时间，当然，这个功能对于部分用户来说非常有用，比如说他们可以查找最近打开的文档啊什么的，但是犹豫这个机制的存在，每次访问文件的时候都要刷写SSD，而大家知道，SSD的寿命是和刷写次数相关的，为了延长寿命，这个功能其实就可以牺牲了。



方法，其实也很简单。在 **/Library/LaunchDaemons** 下添加一个Plist就行了

{% highlight Bash shell scripts %}
sudo vim /Library/LaunchDaemons/com.nullvision.noatime.plist
{% endhighlight %}

然后在里面添加以下内容：

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" 
        "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
    <dict>
        <key>Label</key>
        <string>com.nullvision.noatime</string>
        <key>ProgramArguments</key>
        <array>
            <string>mount</string>
            <string>-vuwo</string>
            <string>noatime</string>
            <string>/</string>
        </array>
        <key>RunAtLoad</key>
        <true/>
    </dict>
</plist>
{% endhighlight %}

然后是设置文件权限

{% highlight Bash shell scripts %}
sudo chown root:wheel /Library/LaunchDaemons/com.nullvision.noatime.plist
{% endhighlight %}

紧接着就可以重启电脑了，重启以后，使用以下命令就可以看到根文件系统被设置noatime权限了

{% highlight Bash shell scripts %}
mount | grep " / "
{% endhighlight %}

可以看到输出结果为

{% highlight Bash shell scripts %}
/dev/disk0s2 on / (hfs, local, journaled, noatime)
{% endhighlight %}

大功告成，妈妈再也不用担心我的SSD磨损太快了。