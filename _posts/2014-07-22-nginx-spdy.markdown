---
layout: post
title:  "给Nginx添加SPDY功能"
date:   2014-07-22 10:56:12
categories: web
---
本人帮协会搭的[论坛](http://forum.opencas.org)，一直是使用ssl访问的，但是普通的https既慢又吃资源，而有个协议可以很方便的解决这个问题，那就是大Google发明的SPDY协议。
所以，我也开始尝试着给自己的论坛加上SPDY协议，WEB服务器本人选择的是nginx，在过去，Nginx并没有内置SPDY协议，需要打开的话还要下载开发版然后手动编译，很不方便。
喜闻乐见的是，最近Nginx发布了1.6稳定版，这个版本终于内置了SPDY的支持，也是我等广大建站人员的福音啊，我也就迫不及待的给论坛加上了SPDY协议支持了。 
  首先明确打开SPDY协议的前提，以下三个缺一不可：

1. Openssl 1.0.1e 或更高版本
2. 网站已经安装了SSL证书
3. Nginx 1.6 stable 或者 1.5Development

### 首先检查Openssl的版本

CentOS 6 可以使用以下命令

{% highlight Bash shell scripts %}
[root@do ~]# rpm -q openssl
openssl-1.0.1e-16.el6_5.14.i686
{% endhighlight %}

可以看到CentOS 6 内置的Openssl已经是满足要求了，如果是CentOS5的话就需要手动升级了 Ubuntu ， Debian 和其他发行版可以用以下命令检查版本

{% highlight Bash shell scripts %}
[root@do ~]# openssl version
OpenSSL 1.0.1e-fips 11 Feb 2013
{% endhighlight %}

### 检查 Nginx 的版本

使用以下命令检查Nginx的版本

{% highlight Bash shell scripts %}
[root@do ~]# nginx -v
nginx version: nginx/1.6.0
{% endhighlight %}

用以下命令查看Nginx里是否包含了SPDY

{% highlight Bash shell scripts %}
[root@do ~]# nginx -V |grep spdy
nginx version: nginx/1.6.0
built by gcc 4.4.7 20120313 (Red Hat 4.4.7-4) (GCC)
TLS SNI support enabled
configure arguments: --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_stub_status_module --with-http_auth_request_module --with-mail --with-mail_ssl_module --with-file-aio --with-ipv6 --with-http_spdy_module --with-cc-opt='-O2 -g -pipe -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector --param=ssp-buffer-size=4 -m32 -march=i386 -mtune=generic -fasynchronous-unwind-tables'
{% endhighlight %}

如果看到--with-http_spdy_module 就说明是满足要求的。 如果没有满足要求，请去 http://nginx.org/ 下载安装1.6 stable

### 打开Nginx的SPDY支持

假设已经在Nginx上配置了SSL的支持，那么打开SPDY就会非常的简单 把以下SSL的配置

{% highlight Bash shell scripts %}
listen [::]:443 ssl;
listen 443 ssl;
{% endhighlight %}

改成

{% highlight Bash shell scripts %}
listen [::]:443 ssl spdy;
listen 443 ssl spdy;
{% endhighlight %}

然后重启Nginx服务  即可 大功告成