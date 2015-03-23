---
layout: post
title:  "CentOS 7 设置时区"
date:   2014-08-15 10:56:12
categories: CentOS
---
在以前的CentOS版本里，时间设置有 **date** ， **hwclock** 等一系列命令，但是CentOS 7 开始，使用了一个统一的命令：

{% highlight Bash shell scripts %}
timedatectl
{% endhighlight %}

这个命令非常的强大，首先是直接使用可以显示当前的系统时间的一些信息：


{% highlight Bash shell scripts %}
~]$ timedatectl
      Local time: Mon 2013-09-16 19:30:24 CEST
  Universal time: Mon 2013-09-16 17:30:24 UTC
        Timezone: Europe/Prague (CEST, +0200)
     NTP enabled: no
NTP synchronized: no
 RTC in local TZ: no
      DST active: yes
 Last DST change: DST began at
                  Sun 2013-03-31 01:59:59 CET
                  Sun 2013-03-31 03:00:00 CEST
 Next DST change: DST ends (the clock jumps one hour backwards) at
                  Sun 2013-10-27 02:59:59 CEST
                  Sun 2013-10-27 02:00:00 CET
{% endhighlight %}

### 设置当前日期：

使用Root执行以下命令就可以了：

{% highlight Bash shell scripts %}
timedatectl set-time YYYY-MM-DD
{% endhighlight %}

### 设置当前时间：

依旧是要Root权限

{% highlight Bash shell scripts %}
timedatectl set-time HH:MM:SS
{% endhighlight %}

默认的，系统是使用UTC时间的，可以用以下命令打开和关闭UTC时间：

{% highlight Bash shell scripts %}
timedatectl set-local-rtc boolean
{% endhighlight %}

把 boolean 替换成yes则表示使用本地时间，替换成no则表示是UTC时间

### 设置时区

可以用以下命令查看所有的时区：

{% highlight Bash shell scripts %}
timedatectl list-timezones
{% endhighlight %}

然后用以下命令设置时区：

{% highlight Bash shell scripts %}
timedatectl set-timezone time_zone
{% endhighlight %}

当然root权限是免不了的

### 与远程NTP服务器同步

timedatectl还可以设置是否打开NTP选项

{% highlight Bash shell scripts %}
timedatectl set-ntp boolean
{% endhighlight %}

同样的，这里的boolean是yes或者no



这个命令的使用就介绍到这里，是不是非常的强大啊。
