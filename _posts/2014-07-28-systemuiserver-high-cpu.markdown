---
layout: post
title:  "解决SystemUIServer占用CPU高的问题"
date:   2014-07-28 9:56:12
categories: Mac
---
这几天用Mac的时候，CPU占有率时不时的会飙升上去。找了很久找不到原因，最后查资料发现，SystemUIServer是处理菜单栏的进程，如果菜单栏有什么要持续更新的，这个进程就会很吃CPU。

然后我检查了一下我的菜单栏，经检查发现我前段时间把钥匙串加到了菜单栏。

关掉就好了。


![关闭钥匙串在菜单栏的显示]({{site_url}}/uploads/2014/07/QQ20140728-1.png)

同时，按照网上的说法，输入法在菜单栏的显示最好也关闭。

![关闭输入法在菜单栏的显示]({{site_url}}/uploads/2014/07/QQ20140728-2.png)

SystemUIServer的CPU占用马上就小下去了。

我这个只是个案例，SystemUIServer的CPU占用率高的同学们，看看是不是菜单栏放了什么东西了，一些没必要的东西，就赶紧将其移出菜单栏吧，尤其是资源大户istat menu。

