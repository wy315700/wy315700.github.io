---
layout: post
title:  "在kindle上阅读APUE"
date:   2012-04-20 03:56:12
categories: jekyll update
---
最近开始学习Linux编程,Stevens 写的APUE当然不能错过。

APUE全称是： **Advanced Programming in the UNIX® Environment: Second Edition**

推荐看英文原版，因为中文版翻译的实在是太烂了，当然如果英语水平不行的人，比如说我，可以中英文配合着看。

言归正传，要在kindle 4上面舒服的看APUE，必要的转换还是需要的。

首先推荐使用CHM版的，因为容易编辑。

我一开始下载了CHM版的以后，直接放到calibre里（用kindle的人应该都听说过这个软件吧）转换成mobi格式的，然后丢kindle里，结果发现一个很大的问题，字体发灰，很灰暗，不够黑，原因是kindle只有16灰阶，书本的字体在电脑上看到是近似于黑色的，但是在kindle里显示不是黑色的。

后来用CHM Editor编辑了一下原来的chm文件

找到docsafari这个文件，把docText标签里的color属性去掉就行了。

![20120420094946](/uploads/2012/04/20120420094946.png)

然后再转换进去，就OK了。

