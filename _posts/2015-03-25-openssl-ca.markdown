---
layout: post
title:  "OpenSSL建立CA"
date:   2015-03-25 21:04:12
categories: web
---

OpenSSL的用法网上有一大堆，大多讲的特别繁琐，在这里将使用OpenSSL建立CA的过程记录一下。

## 生成 RSA 密钥对 {#generate-key}

第一步是建立密钥对：

{% highlight Bash shell scripts %}
$ openssl genrsa -out cakey.pem 2048
{% endhighlight %}

若想对私钥进行加密可以用 `-des3` 参数

## 生成 CA 证书请求 {#generate-request}

{% highlight Bash shell scripts %}
openssl req -new -days 365 -key -sha256 cakey.pem -out careq.pem
{% endhighlight %}

现在都是sha256了，sha1已经被证明是不安全的东西了。

## 对 CA 证书请求进行签名

由于是CA，所以是自签名的

{% highlight Bash shell scripts %}
$ openssl ca -selfsign -in careq.pem -out cacert.pem
{% endhighlight %}

然后就搞定了，生成的cacert.pem就是CA证书

## 一步到位

当然上面的过程还是很繁琐的，有没有一步到位的办法呢，当然是有的。

{% highlight Bash shell scripts %}
openssl req -new -x509 -days 365 -key cakey.pem -out cacert.pem
{% endhighlight %}

当然，key还是要单独生成的。
