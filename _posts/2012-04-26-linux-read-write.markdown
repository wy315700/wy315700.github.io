---
layout: post
title:  "Linux的read和write函数"
date:   2012-04-26 10:56:12
categories: Linux
---
这两个函数都是unbuffered的，也就是直接写入的。

读文件：

函数原型：

{% highlight C %}
#include <unistd.h>

ssize_t read(int fd, void *buf, size_t count);
{% endhighlight %}

read函数尝试从fd所指的文件处读出count数量的字节，然后存到buf所指的缓冲区里。

如果count=0，read()返回0并且不做其他事。如果count比SSIZE_MAX要大,结果是无法预料的。

如果读取成功，read()返回读出的字节数，如果遇到EOF，那么返回0。

当遇到错误的时候，返回-1，以下是相应的错误码说明

 

<table class="table">
<tbody>
<tr>
<td>EAGAIN</td>
<td>没看明白</td>
</tr>
<tr>
<td>EWOULDBLOCK</td>
<td>会被阻塞</td>
</tr>
<tr>
<td>EBADF</td>
<td>fd不是一个打开的文件描述符</td>
</tr>
<tr>
<td>EFAULT</td>
<td>buf不在能读取的内存里</td>
</tr>
<tr>
<td>EINTR</td>
<td>读取文件之前被中断</td>
</tr>
<tr>
<td>EINVAL</td>
<td>挺复杂的，以后再看</td>
</tr>
<tr>
<td>EIO</td>
<td>IO错误</td>
</tr>
<tr>
<td>EISDIR</td>
<td>fd是一个目录</td>
</tr>
</tbody>
</table>


原型：

{% highlight C %}
#include <unistd.h>

ssize_t write(int fd, void *buf, size_t count);
{% endhighlight %}

其他都差不多，需要说明的是：

read 的返回值和count不一致的时候一般认为是到达文件末尾，而write则认为出现错误。

所以一般用read和write的时候都是这么写的：

{% highlight C %}
if(read(fd,buf,count)<count)
{
    ....
}

if(write(fd,buf,count)<count)
{
    perror("Write error !");
    exit(1);
}
{% endhighlight %}