---
layout: post
title:  "Linux 查看 TCP并发数"
date:   2012-04-20 09:56:12
categories: Linux
---
 
查看httpd进程数（即prefork模式下Apache能够处理的并发请求数）：

Linux命令：

{% highlight Bash shell scripts %}
ps -ef | grep httpd | wc -l
{% endhighlight %}
返回结果示例：17

查看Apache的并发请求数及其TCP连接状态：
Linux命令：

{% highlight Bash shell scripts %}
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
{% endhighlight %}
这条命令是网上找的，

下面来分析一下：

首先 netstat -n 就是输出目前所有的连接数，其中 -n 选项表示不解析地址，直接输出数字形式的地址。

然后把输出结果重定向给awk 命令；

awk 命令是一个非常棒的数据处理命令，可以把一行行字分成数个字段来处理。

标准的语法格式是：

{% highlight Bash shell scripts %}
awk '条件类型1{动作1} 条件类型2{动作2}' filename
{% endhighlight %}
当然由于用了管道链接，所以这里的filename就可以省略掉了；

整个awk的处理流程是：

1. 读入一行，并将这一行的数据填入$0,$1等变量里去。

2. 依据条件类型的限制，判断是否要进行后面的动作。

3. 做完所有的动作和条件类型。

4. 如果还有下一行的话，重复1-3的动作。

注意：其中$0表示整行，而$1才是第一个字段。

同时，awk还提供了很多内置变量，方便我们得到总行数和总段数之类的，下面介绍三个变量：

<table class="table" border="1" width="200" align="center">
<tbody>
<tr>
<th>变量名称</th>
<th>代表意义</th>
</tr>
<tr>
<td>NF</td>
<td>每一行拥有的字段总数</td>
</tr>
<tr>
<td>NR</td>
<td>目前awk在处理哪一行</td>
</tr>
<tr>
<td>FS</td>
<td>目前的分隔字符，默认是空格</td>
</tr>
</tbody>
</table>
其中$NF通常用来表示最后一列，而我们要用到的正是最后一列，因为netstat输出中最后一列是连接的状态。

这条命令中，第一个判断条件是 /^tcp/

这是一个正则表达式，就是说寻找以“tcp” 这个字段开始的行，这也是我们需要的，因为netstat除了输出tcp连接还会输出udp以及unix socket连接。

第一个动作是++S[$NF]。

意思是建立一个数组S，其中数组下标是netstat输出的最后一列的字符，就是连接的状态，然后给相应的状态的元素加一；

第二个判断条件是 END

很简单，就是文件结束的时候；

第二个动作就是 for(a in S) print a, S[a]}

就是输出各种状态以及各状态的连接数目。

总得来看，这条命令的作用就是输出系统中tcp连接中各种状态的数目。

返回结果示例：

* TIME_WAIT 8947
* FIN_WAIT1 15
* FIN_WAIT2 1
* ESTABLISHED 55
* SYN_RECV 21
* CLOSING 2
* LAST_ACK 4

其中

<table class="table" border="1" width="200" align="center">
<tbody>
<tr>
<td>LISTEN：</td>
<td>侦听来自远方的TCP端口的连接请求</td>
</tr>
<tr>
<td>SYN-SENT：</td>
<td>再发送连接请求后等待匹配的连接请求</td>
</tr>
<tr>
<td>SYN-RECEIVED：</td>
<td>再收到和发送一个连接请求后等待对方对连接请求的确认</td>
</tr>
<tr>
<td>FIN-WAIT-1：</td>
<td>等待远程TCP连接中断请求，或先前的连接中断请求的确认</td>
</tr>
<tr>
<td>FIN-WAIT-2：</td>
<td>从远程TCP等待连接中断请求</td>
</tr>
<tr>
<td>CLOSE-WAIT：</td>
<td>等待从本地用户发来的连接中断请求</td>
</tr>
<tr>
<td>CLOSING：</td>
<td>等待远程TCP对连接中断的确认</td>
</tr>
<tr>
<td>LAST-ACK：</td>
<td>等待原来的发向远程TCP的连接中断请求的确认</td>
</tr>
<tr>
<td>TIME-WAIT：</td>
<td>等待足够的时间以确保远程TCP接收到连接中断请求的确认</td>
</tr>
<tr>
<td>CLOSED：</td>
<td>没有任何连接状态</td>
</tr>
</tbody>
</table>

 