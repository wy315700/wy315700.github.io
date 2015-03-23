---
layout: post
title:  "各种系统清除DNS缓存的方法!"
date:   2015-01-22 20:56:12
categories: Other
---
收集一下，以下所有命令都在命令行里完成。

OSX 10.10 Yosemite

{% highlight Bash shell scripts %}
sudo discoveryutil udnsflushcaches
{% endhighlight %}

OSX 10.9

{% highlight Bash shell scripts %}
dscacheutil -flushcache; sudo killall -HUP mDNSResponder
{% endhighlight %}

OSX 10.7  – 10.8

{% highlight Bash shell scripts %}
sudo killall -HUP mDNSResponder
{% endhighlight %}

OSX 10.5 – 10.6

{% highlight Bash shell scripts %}
sudo dscacheutil -flushcache
{% endhighlight %}

Windows

{% highlight Bash shell scripts %}
ipconfig /flushdns
{% endhighlight %}

Linux(取决于系统)

{% highlight Bash shell scripts %}
/etc/init.d/named restart
或
/etc/init.d/nscd restart
{% endhighlight %}

 

 

 
