---
layout: post
title:  "windows编程中的错误处理"
date:   2012-06-24 10:56:12
categories: Windows
---
前不久写了[Linux编程中的错误处理]({% post_url 2012-05-21-linux-error-handing %})，以及所有的错误代码。下面来看看Windows 里错误是怎么处理的。

{% highlight C %}
DWORD WINAPI GetLastError(void);
{% endhighlight %}

一般来说，WINDOWS函数检测到错误的时候，都会设置一个错误代码，而这个函数就是用来获取最后的错误代码的，此函数是线程安全的。

而在调试的时候，可以在visual studio 里的监视窗口设置一个 “@err,hr”变量，就能监视最后发生的错误代码了。

![20120624104047](/uploads/2012/06/20120624104047.png)

如图所示，监视窗口向我们展示了错误编号，以及该编号对应的细节。

同时，visual studio 都会提供一个小工具用于把错误编号转换成错误原因。

微软还提供了一个函数用于格式化错误输出：

{% highlight C %}
DWORD WINAPI FormatMessage(
  __in      DWORD dwFlags,
  __in_opt  LPCVOID lpSource,
  __in      DWORD dwMessageId,
  __in      DWORD dwLanguageId,
  __out     LPTSTR lpBuffer,
  __in      DWORD nSize,
  __in_opt  va_list *Arguments
);
{% endhighlight %}

这个函数功能非常丰富。

同时，应用程序也能自己定义错误代码，

{% highlight C %}
void WINAPI SetLastError(
  __in  DWORD dwErrCode
);
{% endhighlight %}