---
layout: post
title:  "CentOS 更新系统时不更新内核"
date:   2014-07-28 10:56:12
categories: CentOS
---
今天在使用Ucloud的云服务器的时候，手贱使用了一下 `yum upgrade -y`

然后重启以后我的机器就连不上了。

与客服人员交流后发现是更新的时候不能更新内核。

其实方法也很简单，更新的时候使用以下命令就行了


{% highlight Bash shell scripts %}
yum -y --exclude=kernel\* upgrade
{% endhighlight %}

或者

{% highlight Bash shell scripts %}
yum -y -x 'kernel*' upgrade
{% endhighlight %}

当然，也可以写入到配置文件里，一劳永逸。

{% highlight Bash shell scripts %}
vim /etc/yum.conf
{% endhighlight %}

写入以下信息

{% highlight Bash shell scripts %}
exclude=kernel*
{% endhighlight %}

大功告成，妈妈在也不用担心更新系统后无法启动了。