---
layout: post
title:  "htop 不重复显示进程"
date:   2014-07-26 10:56:12
categories: Linux
---
在使用htop查看进程信息的时候，经常会出现很多个进程重复的情况，很是烦人，比如说以下情况：

![QQ20140726-1]({{site_url}}/uploads/2014/07/QQ20140726-1.png)

经研究发现，htop会把一个进程里的线程当做一个进程来显示出来，上图中的unicorn woker[1] 一共有3个线程，所以htop显示了三个进程。

这个特性对于分析进程性能很不有利， 所以我们要关掉它。

好在htop也是提供了一个方法来设置这个选项。


按F2
选择 Display options
选择 Hide userland threads
如下图所示

![QQ20140726-2]({{site_url}}/uploads/2014/07/QQ20140726-2.png)

大功告成，这样以后，在htop的进程列表里再也看不见一坨重复的进程了

![QQ20140726-3]({{site_url}}/uploads/2014/07/QQ20140726-3.png)

