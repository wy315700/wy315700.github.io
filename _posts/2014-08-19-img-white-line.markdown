---
layout: post
title:  "图片底部出现空行"
date:   2014-08-19 10:56:12
categories: web
---
在写html的时候，经常有以下写法

{% highlight html %}
<div style="font-size:0;">
  <p  style="padding-top:0px; text-align:center;" ><img src="xxxx/1_02.jpg" width="500px;" /></p></div>
<div>
{% endhighlight %}

但是这种情况下，在img底部会出现一个空行，很难看。

![QQ20140819-1]({{ site.url }}/uploads/2014/08/QQ20140819-1.png)

要解决这个问题，其实也很简单，给img标签加一个属性就行了：


{% highlight css %}
img{vertical-align: top;}
{% endhighlight %}

然后问题就解决了

![QQ20140819-2]({{ site.url }}/uploads/2014/08/QQ20140819-2.png)


妈妈在也不用担心图片之间有空行了

 
