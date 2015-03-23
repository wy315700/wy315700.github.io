---
layout: post
title:  "Python 自定义信号处理"
date:   2014-07-04 10:56:12
categories: Programing
---
有时候，我们写程序需要处理一些信号，比如在程序终止的时候写入到日志文件，比如截获Ctrl + C事件，这个时候就需要用自定义的信号处理方法了。

用到的函数是：

{% highlight Python %}
signal.signal(signalnum, handler)
{% endhighlight %}

这个函数是对于Unix里的signal函数的一个简单包装。


signalnum是你需要处理的信号值，具体可以见Unix的signal说明，当然需要说明一下，在Windows下，只允许使用以下信号：

{% highlight Python %}
SIGABRT, SIGFPE, SIGILL, SIGINT, SIGSEGV, SIGTERM
{% endhighlight %}

不然会报<code style="color: red;">ValueError</code>的错误。

同时，这个函数会返回上一个对于该信号处理的handler。

handler就是指对应的处理信号的函数，函数原型如下所示

{% highlight Python %}
def handler(signum, frame):
    pass
{% endhighlight %}

其中signum就是发生的信号，frame是发生信号的时候的函数调用栈。

当然，如果想在多线程程序里处理信号的话，<code style="color: red;">必须在主线程里捕获。</code>

以下是示例代码：

{% highlight Python %}
#!/usr/local/bin/python
#-*- coding: utf-8 -*-
import re,sys,re
import string
import signal
 
def sigint_handler(signum, frame):
  global is_sigint_up
  is_sigint_up = True
  print 'catched interrupt signal!'
 
#
signal.signal(signal.SIGINT, sigint_handler)
 
#以下那句在windows python2.4不通过,但在freebsd下通过
signal.signal(signal.SIGHUP, sigint_handler)
 
signal.signal(signal.SIGTERM, sigint_handler)
is_sigint_up = False
 
# 循环
while True:
  try:
    # 你想做的事情
  
    if is_sigint_up:
     # 中断时需要处理的代码
      print "Exit"
      break
  except Exception, e:
    #  这里发生错误时需要写的代码
    break
 {% endhighlight %}