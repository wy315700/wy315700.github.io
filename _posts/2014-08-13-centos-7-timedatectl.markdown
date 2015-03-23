---
layout: post
title:  "CentOS 7 设置时间"
date:   2014-08-13 10:56:12
categories: CentOS
---
在过去的CentOS版本里，要设置时区的话要手动修改 **/etc/locale** .conf文件，很是麻烦，不过CentOS 7 已经为我们准备好一个非常强大的工具了： **localectl**

## 显示当前时区

使用以下命令：

{% highlight Bash shell scripts %}
~]$ localectl status
   System Locale: LANG=en_US.UTF-8
       VC Keymap: us
      X11 Layout: n/a
{% endhighlight %}


可以看到，除了显示了系统的时区信息以外，还显示了键盘信息和X11布局信息



## 列出所有的时区

用以下命令显示所有的英文时区

{% highlight Bash shell scripts %}
~]$ localectl list-locales | grep en_
en_AG
en_AG.utf8
en_AU
en_AU.iso88591
en_AU.utf8
en_BW
en_BW.iso88591
en_BW.utf8
{% endhighlight %}


如果要显示中文的，只需要把grep en  改成grep zh就行了

## 设置时区

使用Root执行以下命令：

{% highlight Bash shell scripts %}
localectl set-locale LANG=locale
{% endhighlight %}


把最后的 *locale* 替换成具体的时区，比如zh_CN.UTF-8就可以设置了。

是不是很强大啊。
