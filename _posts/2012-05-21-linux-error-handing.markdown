---
layout: post
title:  "Linux编程中的error处理"
date:   2012-05-21 09:56:12
categories: Linux
---
函数调用的时候，如果发生错误，通常会返回一个负整数值，通常这个数是-1，或者NULL。但是这样的话就很难知道发生了什么错误，Linux里用了一个变量errno来处理这种情况。当发生错误的时候，通常把错误代号赋给errno。比如一个open函数，就有18个错误代码（见此）。

很早的时候，errno 是这么定义的：

{% highlight C %}
extern int errno
{% endhighlight %}

在单线程的程序里当然没问题，但是遇到多线程的时候，问题就来了。 因为errno是全局变量，当你调用一个函数时，发现这个函数发生了错误，但当你使用错误原因时，他却变成了另外一个线程的错误提示。想想就会觉得是件可怕的事情。
将errno设置为线程局部变量是个不错的主意，事实上，GCC中就是这么干的。他保证了线程之间的错误原因不会互相串改，当你在一个线程中串行执行一系列过程，那么得到的errno仍然是正确的。

bits/errno.h的定义：

{% highlight C %}
# ifndef __ASSEMBLER__
/* Function to get address of global `errno' variable.  */
extern int *__errno_location (void) __THROW __attribute__ ((__const__));

#  if !defined _LIBC || defined _LIBC_REENTRANT
/* When using threads, errno is a per-thread value.  */
#   define errno (*__errno_location ())
#  endif
# endif /* !__ASSEMBLER__ */
{% endhighlight %}

而errno.h中是这样定义的：

{% highlight C %}
/* Declare the `errno' variable, unless it's defined as a macro by
   bits/errno.h.  This is the case in GNU, where it is a per-thread
   variable.  This redeclaration using the macro still works, but it
   will be a function declaration without a prototype and may trigger
   a -Wstrict-prototypes warning.  */
#ifndef errno
extern int errno;
#endif
{% endhighlight %}

这样定义的话，既实现了多线程环境下的线程安全性，又实现了C的标准。

而具体的errno的值和错误代号之间的关系在 asm/errno.h和asm-generic/errno.h 下。

当然，Linux也提供了两个函数来翻译errno。

一个是 strerror;还有一个是perror，先看定义：

{% highlight C %}
#include <string.h>

 char *streeor(int errno);
{% endhighlight %}

{% highlight C %}
#include <stdio.h>

  void perror(const char *msg);
{% endhighlight %}

strerror的作用主要是把errno翻译成字符串，而perror是直接在标准错误输出里把翻译后的字符串输出。

当然，有了错误不一定要退出程序，比如以下错误：

EAGAIN,ENFILE,ENOBUFS,ENOLCK,ENOSPC,ENOSR,EWOULDBLOCK，有时候ENOMEM,EBUSY也是。

这几个错误主要是源于多进程环境下的资源竞争和资源不足，等其他进程释放资源后就能继续运行了。

 [Linux 内置的errno代号在此。]({% post_url 2012-05-21-linux-all-errno %})