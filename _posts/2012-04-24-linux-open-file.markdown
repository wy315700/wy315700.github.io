---
layout: post
title:  "Linux打开文件函数"
date:   2012-04-24 09:56:12
categories: Linux
---
man open 里东西好多,终于都看了一遍.

转载请注明出处

函数原型：

{% highlight C %}
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int open(const char *pathname, int flags);
int open(const char *pathname, int flags, mode_t mode);

int creat(const char *pathname, mode_t mode);
{% endhighlight %}

# 参数说明：

其中flags必须包括以下一些参数：

<table class="table">
<tbody>
<tr>
<td>O_RDONLY</td>
<td>只读模式打开</td>
</tr>
<tr>
<td>O_WRONLY</td>
<td>只写模式打开</td>
</tr>
<tr>
<td>O_RDWR</td>
<td>读写都可以</td>
</tr>
</tbody>
</table>
还可以包括以下一些参数：

<table class="table">
<tbody>
<tr>
<td>O_APPEND</td>
<td> 当加入这个标志以后，每次写入文件的时候都会从末尾写入。</td>
</tr>
<tr>
<td>O_CREAT</td>
<td>当文件不存在的时候创建新文件。</td>
</tr>
<tr>
<td>O_EXCL</td>
<td>当O_CREAT标志存在且文件存在的时候输出错误。</td>
</tr>
<tr>
<td>O_TRUNC</td>
<td>当文件存在且被另一个程序以可写模式打开时，把文件的长度截短为0（不明白什么意思），如果是从FIFO（先入先出）设备读取的，那么忽略这个参数。</td>
</tr>
<tr>
<td>O_NOCTTY</td>
<td>如果pathname是一个终端，则不将该设备分配给进程作为控制终端</td>
</tr>
<tr>
<td>O_NONBLOCK</td>
<td>如果可以，比如打开一个FIFO文件，以非阻塞的形式打开文件（对文件进行写入的时候不暂停进程）。</td>
</tr>
</tbody>
</table>
注：当O_CREAT参数有效的时候，mode参数有效，用于表示创建的文件的权限；

由mode & ~umask得到文件的最终权限；一般umask都是022。

其中mode中常用常数是：

<table class="table">
<tbody>
<tr>
<td>S_IRWXU</td>
<td>00700</td>
<td>拥有者可写可读可执行</td>
</tr>
<tr>
<td>S_IRUSR</td>
<td>00400</td>
<td>拥有者可读</td>
</tr>
<tr>
<td>S_IWUSR</td>
<td>00200</td>
<td>拥有者可写</td>
</tr>
<tr>
<td>S_IXUSR</td>
<td>00100</td>
<td>拥有者可执行</td>
</tr>
<tr>
<td>S_IRWXG</td>
<td>00070</td>
<td>群组可写可读可执行</td>
</tr>
<tr>
<td>S_IRGRP</td>
<td>00040</td>
<td>群组可读</td>
</tr>
<tr>
<td>S_IWGRP</td>
<td>00020</td>
<td>群组可写</td>
</tr>
<tr>
<td>S_IXGRP</td>
<td>00010</td>
<td>群组可执行</td>
</tr>
<tr>
<td>S_IRWXO</td>
<td>00007</td>
<td>其他可写可读可执行</td>
</tr>
<tr>
<td>S_IROTH</td>
<td>00004</td>
<td>其他可读</td>
</tr>
<tr>
<td>S_IWOTH</td>
<td>00002</td>
<td>其他可写</td>
</tr>
<tr>
<td>S_IXOTH</td>
<td>00001</td>
<td>其他可执行</td>
</tr>
</tbody>
</table>
 

除了上述参数以外，flags还有以下参数（来着 man open)

<table class="table">
<tbody>
<tr>
<td>O_ASYNC</td>
<td>当文件可读或者可写的时候发送一个信号</td>
</tr>
<tr>
<td>O_CLOEXEC</td>
<td>没看明白</td>
</tr>
<tr>
<td>O_DIRECT</td>
<td>最小化缓存的影响，尽量直接写入文件。</td>
</tr>
<tr>
<td>O_DIRECTORY</td>
<td>当pathname不是一个目录的时候，打开失败</td>
</tr>
<tr>
<td>O_LARGEFILE</td>
<td>允许文件大小超过off_t规定的大小，但是不得超过off64_t</td>
</tr>
<tr>
<td>O_NOATIME</td>
<td>读取文件的时候不更新最近打开时间</td>
</tr>
<tr>
<td>O_NOFOLLOW</td>
<td>如果pathname是一个符号链接，那么打开失败。</td>
</tr>
<tr>
<td>O_SYNC</td>
<td>以缓存的形式打开文件</td>
</tr>
</tbody>
</table>
creat 函数已经没什么用了  ，open 加上 O_CREAT|O_WRONLY|O_TRUNC三个参数就可以取代creat了。

返回值：当函数正确运行的时候，返回对应的文件描述符，当错误的时候返回-1，同时设置errno。

以下是常用的errno:

 

<table class="table">
<tbody>
<tr>
<td>EACCES</td>
<td>程序无法访问文件或者权限不足</td>
</tr>
<tr>
<td>EEXIST</td>
<td>文件存在且O_CREAT和O_EXCL参数被选择</td>
</tr>
<tr>
<td>EFAULT</td>
<td>pathname超出能访问的内存</td>
</tr>
<tr>
<td>EFBIG</td>
<td>见EOVERFLOW</td>
</tr>
<tr>
<td>EINTR</td>
<td>等待一个很慢的设备，或者被signal(7）打断</td>
</tr>
<tr>
<td>EISDIR</td>
<td>尝试对目录写入</td>
</tr>
<tr>
<td>ELOOP</td>
<td>符号链接太多了</td>
</tr>
<tr>
<td>EMFILE</td>
<td>打开文件数到达最大值</td>
</tr>
<tr>
<td>ENODEV</td>
<td>对应的设备不存在</td>
</tr>
<tr>
<td>ENOENT</td>
<td>O_CREAT没被使用并且文件不存在</td>
</tr>
<tr>
<td>ENOMEM</td>
<td>内核内存不足</td>
</tr>
<tr>
<td>ENOSPC</td>
<td>设备没有多余的空间</td>
</tr>
<tr>
<td>ENXIO</td>
<td>O_NONBLOCK | O_WRONLY被设置并且该文件是一个FIFO并且没有程序读取，或者文件是一个没有响应的设备</td>
</tr>
<tr>
<td>EOVERFLOW</td>
<td>文件太大</td>
</tr>
<tr>
<td>EPOFS</td>
<td>O_NOATIME被设置，但是进程没有权限这么做</td>
</tr>
<tr>
<td>EROFS</td>
<td>在只读设备上尝试写入</td>
</tr>
<tr>
<td>ETXTBSY</td>
<td>试图写入一个正在执行的文件</td>
</tr>
<tr>
<td>EWOULDBLOCK</td>
<td>当O_BLOCK被设置,但是设备不兼容</td>
</tr>
</tbody>
</table>
<h3><strong>附注：</strong></h3>
附注：

O_RDONLY,O_WRONLY和O_RDWR三个参数不能共存.

在POSIX定义了三种同步方式，O_SYNC,O_DSYNC,O_RSYNC,但是在Linux里,这三种都是同步的写入方式.