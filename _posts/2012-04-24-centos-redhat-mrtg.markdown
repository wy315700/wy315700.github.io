---
layout: post
title:  "在CentOS(RedHat)6.2上安装mrtg"
date:   2012-04-24 10:56:12
categories: CentOS
---
mrtg 是一个非常流行的流量统计工具。虽然看起来有点过时了，当然也有以下几点好处：

1. 我只有一台机器要用,比如我的个人网站.为了一个小服务器搞个cacti不值.象这样MRTG还是很方便的.
2. 就算大面积使用Cacti加RRD还是有必要在本机运行一个可以直接查看的网页比较方便.方便运维排错.
3. 可以在一个节点的一台机器上装一个MRTG,然后加上那个节点后面所有的机器,这样可以显示每个节点的流量,方便节点排错.
 
 
MRTG的全称叫 Multi Router Traffic Grapher 可以监控很多东西,今天我们就用它来监控我小小的个人网站的流量.节点之类多设备的设置后面也可以参考一下.

### 第一步：安装mrtg,snmp等工具

{% highlight Bash shell scripts %}
yum install mrtg net-snmp net-snmp-utils
{% endhighlight %}

### 第二步：配置 snmpd

不建议自己配置，可以先运行以下命令

{% highlight Bash shell scripts %}
snmpconf
{% endhighlight %}

运行完命令后什么都不要设置，直接退出，这样conf文件应该就生成了，下面修改几个地方：

把

{% highlight Bash shell scripts %}
access  notConfigGroup ""      any       noauth    exact systemview none none
{% endhighlight %}

改成

{% highlight Bash shell scripts %}
access  notConfigGroup ""      any       noauth    exact all none none
{% endhighlight %}

然后添加

{% highlight Bash shell scripts %}
view all    included  .1                               80
{% endhighlight %}

注：

com2sec notConfigUser  localhost       public 这个后面二个选项是指,可以取得信息的地址为 Localhost,使用的验证码为 public

access  notConfigGroup ""      any       noauth    exact all none none 这行中,会打开读信息.可以读取所有的信息,倒数第三个选项 all 来指定.

记的重启服务

{% highlight Bash shell scripts %}
service snmpd restart
{% endhighlight %}

然后来确认一下我们的配置,用下面的命令,看看能不能得到你接口的ip信息

{% highlight Bash shell scripts %}
snmpwalk -v 1 -c public localhost IP-MIB::ipAdEntIfIndex
{% endhighlight %}

如果看到以下几行说明成功了：

{% highlight Bash shell scripts %}
IP-MIB::ipAdEntIfIndex.127.0.0.1 = INTEGER: 1
IP-MIB::ipAdEntIfIndex.202.120.107.153 = INTEGER: 2
{% endhighlight %}

这里的IP就是你的服务器IP。

### 第三步:配置MRTG

{% highlight Bash shell scripts %}
 cfgmaker --global 'WorkDir: /var/www/mrtg'  
          --global 'Options[_]: bits,growright' 
          --output /etc/mrtg/mrtg.cfg    
           public@localhost
{% endhighlight %}

其中

    * –global 'WorkDir: /var/www/mrtg' : 设置全局的工作目录配置,也就是存MRTG的图象的地址
    * –global "Options[_]: growright,bits" :设置网络显示，growright就是从左到又
    * –output /etc/mrtg/mrtg.cfg: 你输出的配置文件的地址
    * public@localhost : public是你的snmp设备读的密码,localhost是设备的密码.如果你要显示远程的snmp的设备,就是远程的地址的密码,现在我这是本地的.
设置完后,运行indexmaker来建立网页显示接口的信息.这个只需运行一次,你加入新的设备和新监控内容才需要更新.

{% highlight Bash shell scripts %}
 indexmaker --output=/var/www/mrtg/index.html /etc/mrtg/mrtg.cfg
{% endhighlight %}

然后运行mrtg

{% highlight Bash shell scripts %}
mrtg /etc/mrtg/mrtg.cfg
{% endhighlight %}

第一次运行会报错，多运行几次就好了。

### 第四步:加入定时任务

如果是用yum安装的，那么定时任务已经添加了。

最后你打开你的网站的

http://your-ip.add.ress/mrtg/

