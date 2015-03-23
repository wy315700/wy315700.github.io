---
layout: post
title:  "wp super cache 后台XSS"
date:   2013-01-24 10:56:12
categories: web
---
这篇文章是我写的，但是考虑到本人的博客的影响力，所以首发选择了 [FREEBUF](http://www.freebuf.com/vuls/6688.html){:target="_blank"}

WP SUPER CACHE 是一款很流行的缓存插件 ，在其后台有个功能，可以查看所有的缓存文件，其中缓存文件分为wp-cached缓存 和wp-super-cache缓存。

其原理是，当检测到有 `comment_author_|wordpress_logged_in|wp-postpass_` 三个开头的cookies的时候缓存工作交给wp-cached，其他情况下由wp-super-cache负责缓存

![20130104150329_43602]({{site_url}}/uploads/2013/01/20130104150329_43602.png)

其中 wp-cached缓存文件里有个字段叫密钥，经研究，该密钥是由以下几部分组成： **域名+端口+(wordpress_logged_in开头的cookies)+post数据** 

其中post字段过滤很严，但是cookies字段没有过滤一些东西，于是可以仿造以下请求

设置 cookies为

{% highlight JavaScript %}
wordpress_logged_in_a =  <ScRiPt >alert(111111111111);</ScRiPt>
{% endhighlight %}

即可

![20130104151352_47238]({{site_url}}/uploads/2013/01/20130104151352_47238.png)

chrome 可以用 [Edit This Cookie](https://chrome.google.com/webstore/detail/edit-this-cookie/fngmhnnpilhplaeedifhccceomclgfbg){:target="_blank"} 来设置cookies

用起来还是比较鸡肋的，一方面wp-cached会隔一段时间去清理缓存，另一方面，得让管理员去点击查看已缓存内容。

不过这个漏洞可以让普通访问者去跨管理员，相对而言危险性也比较高吧。

是一个高危险但是出发条件比较困难的漏洞。

