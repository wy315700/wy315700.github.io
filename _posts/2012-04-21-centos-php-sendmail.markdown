---
layout: post
title:  "CentOS 解决PHP无法sendmail"
date:   2012-04-21 10:56:12
categories: CentOS
---
首先是安装sendmail

{% highlight Bash shell scripts %}
yum install sendmail sendmail-cf
{% endhighlight %}

然后修改php.ini 找到 sendmail_path，

改成

{% highlight Bash shell scripts %}
sendmail_path = /usr/sbin/sendmail -t -i
{% endhighlight %}

发现还是不能发送，用探针的时候提示发送失败，

用wordpress 发送邮件的时候提示：

电子邮件无法发送。
可能原因：您的主机禁用了 mail() 函数…
后来查看 /var/log/maillog 的 时候发现了这么一条

{% highlight Bash shell scripts %}
Apr 21 17:26:22 server sendmail[2320]: NOQUEUE: SYSERR(apache): can not chdir(/var/spool/clientmqueue/): Permission denied
{% endhighlight %}

居然没有权限，后来想明白了，是SELinux干的。

用  `getsebool -a | grep mail` 命令查看权限

发现了这么一条

{% highlight Bash shell scripts %}
httpd_can_sendmail --> off
{% endhighlight %}

于是一切都明白了

用下面的命令改一下就成功了

{% highlight Bash shell scripts %}
 setsebool -P httpd_can_sendmail on
{% endhighlight %}

终于可以发送邮件了

于是

 