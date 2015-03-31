---
layout: post
title:  "Nginx做反向代理时缓存问题总结"
date:   2015-03-31 17:00:12
categories: web
keywords: Nginx,Proxy,inactive,proxy_cache_valid,expires,Partial Requests,Byte Range
---

今天给 [中科院镜像服务器](http://www.opencas.org/){:target="_blank"} 做了双机负载均衡，使用Nginx进行反向代理，遇到一些缓存的问题，总结如下。

### 缓存过期时间

在配置的时候，有如下三个地方可以设置缓存过期时间：

* inactive=1d
* proxy_cache_valid 200 304 1h
* expires 10m

其实解释起来很简单：

* inactive=1d 是指多久未访问以后清除缓存
* proxy_cache_valid 200 304 1h 是指距离缓存产生时间多久以后清除缓存
* expires 10m 这个不是控制服务器端的，而是指在Http Response header里指定的过期时间，是给客户端看的。

### temp的问题

Nginx进行反代的时候，遇到超出文件大小 `proxy_buffer_size` 的时候，是一次性把文件都加载到Temp目录，然后再发送给用户。

如果设置了 `proxy_buffering off` 则不会加载到Temp目录，而是同步的从上游进行加载。

可以通过设置 `proxy_max_temp_file_size` 参数来设置最大可以缓存的文件大小。

### 206 和 Byte Range 的问题

Byte Range允许客户端向服务器请求一部分文件，而不是整个文件。大部分支持多线程下载和断点下载的软件都是用的这个功能。这个时候服务器返回的Http Code是206 Partial Requests.

但是Nginx做反代的时候，如果没有好好的设置，这个功能可能会引来Dos攻击。

因为默认做反代的时候，Nginx向后端服务器请求的时候是不会把 `Range` 参数加上的，而是会去请求整个文件，比方说有一个1G的文件，每次请求1M，Nginx会在每次请求的时候去后端请求一个完整的1G文件，然后取出其中的1M发给客户端，这个时候中间的流量会暴增，导致整个服务器宕机。今天因为这个问题导致我检查了很久。

解决方案也很简单，把 `Range` 加到Header里就行了。

{% highlight Bash shell scripts %}
proxy_set_header Range $http_range;
proxy_set_header If-Range $http_if_range;
proxy_no_cache $http_range $http_if_range;
{% endhighlight %}