---
layout: post
title:  "windows里防止程序多实例运行"
date:   2012-06-21 10:56:12
categories: Windows
---
前段时间看了 [Linux防止多实例运行的例子]({% post_url 2012-05-31-linux-disable-multi-instance %})，用的是文件锁，而windows里可以创建一个互斥的内核对象来达到这个效果。

先看代码


{% highlight C %}
#include<windows.h>
#include <stdio.h>
#include <stdlib.h>
int main()
{
    HANDLE mutex;

    mutex=CreateMutex(NULL,false,"testmutex");
    if (GetLastError() == ERROR_ALREADY_EXISTS)
    {
        CloseHandle(mutex);
        exit(1);
    };
    system("pause");

    CloseHandle(mutex);
}
{% endhighlight %}

这段代码创建了一个互斥命名对象，如果对象已存在，那么程序退出。
CreateMutex 函数的原型是：

{% highlight C %}
HANDLE WINAPI CreateMutex(
  __in_opt  LPSECURITY_ATTRIBUTES lpMutexAttributes,
  __in      BOOL bInitialOwner,
  __in_opt  LPCTSTR lpName
);
{% endhighlight %}

这个程序接受三个参数：

 lpMutexAttributes [in, optional]：

一个指向 SECURITY_ATTRIBUTES 结构的指针，如果这个参数为NULL，那么该内核对象将获得一个默认的安全描述符，且不能为子进程继承。

bInitialOwner [in]：

如果值为true 那么创建的线程将获得该对象的拥有权，否则不拥有。

lpName [in, optional]：

对象的名称，名称的最大值为 **MAX_PATH**  。

如果该名称与一个已经存在的互斥量对象相同，这个函数需要 **MUTEX_ALL_ACCESS** 权限，这个时候，bInitialOwner 参数会被忽略，如果lpMutexAttributes 非空，它将觉得该句柄是否可继承，但是security-descriptor 成员会被忽略。

如果lpName是NULL，这个对象将是一个匿名对象。

如果该名称与一个已经存在的 event, semaphore, waitable timer, job, 或者 file-mapping 对象相同，GetLastError将返回 **ERROR_INVALID_HANDLE** 。

该对象可以被创建在一个秘密的命名空间，参考 [Object Namespaces](http://msdn.microsoft.com/en-us/library/windows/desktop/ms684295(v=vs.85).aspx){:target="_blank"}

返回值：

如果创建成功，将返回创建的句柄。

如果失败，将返回NULL，并且可以用GetLastError获取错误。

如果这个mutex已经存在，GetLastError将返回ERROR_ALREADY_EXISTS，这个正可以用来当防止多实例运行的方法。同时，如果权限不够，将返回ERROR_ACCESS_DENIED 。

 

 