---
layout: post
title:  "windows编程中所有的错误代码"
date:   2012-06-25 10:56:12
categories: Windows
---
上一篇文章里写了 [windows编程中的错误处理]({% post_url 2012-06-24-windows-error-handing %}) ,花了点时间写了个程序把错误代码都解释出来了。

{% highlight C %}
#ifndef UNICODE
#define UNICODE
#endif
#include &lt;locale&gt;
#include &lt;windows.h&gt;
#include &lt;stdio.h&gt;
#include &lt;stdlib.h&gt;

void main(void)
{
    char err[200];
    WCHAR buffer[200];
    FILE *fp;
    setlocale(LC_ALL, "chs");
    int errnum;
    freopen("err.txt","r",stdin);
    fp=fopen("ans.txt","w");

    printf("&lt;table class="table"&gt; &lt;tbody&gt; &lt;th&gt;错误代码&lt;/th&gt;&lt;th&gt;错误编号&lt;/th&gt;&lt;th&gt;错误内容&lt;/th&gt;");
    while(gets(err)!=NULL)
    {
        while(strlen(err)==0)gets(err);
        while(!isupper(err[1]))gets(err);
        scanf("%d",&amp;errnum);
        FormatMessage(FORMAT_MESSAGE_FROM_SYSTEM | FORMAT_MESSAGE_ARGUMENT_ARRAY |FORMAT_MESSAGE_IGNORE_INSERTS,
            NULL, 
            errnum,
            LANG_NEUTRAL,
            buffer, 
            200, 
            NULL);

        printf("&lt;tr&gt;%s&lt;/tr&gt; &lt;tr&gt;%d&lt;/tr&gt; &lt;tr&gt;%ls&lt;/tr&gt;",err,errnum,buffer);
        memset(buffer,0,sizeof(buffer));
        memset(err,0,sizeof(err));
    }
    printf("&lt;/tbody&gt;&lt;/table&gt; ");
//  system("pause");
}
{% endhighlight %}

<table class="table">
<tbody>
<tr>
<td>ERROR_SUCCESS</td>
<td>0</td>
<td>操作成功完成。</td>
</tr>
<tr>
<td>ERROR_INVALID_FUNCTION</td>
<td>1</td>
<td>函数不正确。</td>
</tr>
<tr>
<td>ERROR_FILE_NOT_FOUND</td>
<td>2</td>
<td>系统找不到指定的文件。</td>
</tr>
<tr>
<td>ERROR_PATH_NOT_FOUND</td>
<td>3</td>
<td>系统找不到指定的路径。</td>
</tr>
<tr>
<td>ERROR_TOO_MANY_OPEN_FILES</td>
<td>4</td>
<td>系统无法打开文件。</td>
</tr>
<tr>
<td>ERROR_ACCESS_DENIED</td>
<td>5</td>
<td>拒绝访问。</td>
</tr>
<tr>
<td>ERROR_INVALID_HANDLE</td>
<td>6</td>
<td>句柄无效。</td>
</tr>
<tr>
<td>ERROR_ARENA_TRASHED</td>
<td>7</td>
<td>存储控制块被损坏。</td>
</tr>
<tr>
<td>ERROR_NOT_ENOUGH_MEMORY</td>
<td>8</td>
<td>存储空间不足，无法处理此命令。</td>
</tr>
<tr>
<td>ERROR_INVALID_BLOCK</td>
<td>9</td>
<td>存储控制块地址无效。</td>
</tr>
<tr>
<td>ERROR_BAD_ENVIRONMENT</td>
<td>10</td>
<td>环境不正确。</td>
</tr>
<tr>
<td>ERROR_BAD_FORMAT</td>
<td>11</td>
<td>试图加载格式不正确的程序。</td>
</tr>
<tr>
<td>ERROR_INVALID_ACCESS</td>
<td>12</td>
<td>访问码无效。</td>
</tr>
<tr>
<td>ERROR_INVALID_DATA</td>
<td>13</td>
<td>数据无效。</td>
</tr>
<tr>
<td>ERROR_OUTOFMEMORY</td>
<td>14</td>
<td>存储空间不足，无法完成此操作。</td>
</tr>
<tr>
<td>ERROR_INVALID_DRIVE</td>
<td>15</td>
<td>系统找不到指定的驱动器。</td>
</tr>
<tr>
<td>ERROR_CURRENT_DIRECTORY</td>
<td>16</td>
<td>无法删除目录。</td>
</tr>
<tr>
<td>ERROR_NOT_SAME_DEVICE</td>
<td>17</td>
<td>系统无法将文件移到不同的磁盘驱动器。</td>
</tr>
<tr>
<td>ERROR_NO_MORE_FILES</td>
<td>18</td>
<td>没有更多文件。</td>
</tr>
<tr>
<td>ERROR_WRITE_PROTECT</td>
<td>19</td>
<td>介质受写入保护。</td>
</tr>
<tr>
<td>ERROR_BAD_UNIT</td>
<td>20</td>
<td>系统找不到指定的设备。</td>
</tr>
<tr>
<td>ERROR_NOT_READY</td>
<td>21</td>
<td>设备未就绪。</td>
</tr>
<tr>
<td>ERROR_BAD_COMMAND</td>
<td>22</td>
<td>设备不识别此命令。</td>
</tr>
<tr>
<td>ERROR_CRC</td>
<td>23</td>
<td>数据错误(循环冗余检查)。</td>
</tr>
<tr>
<td>ERROR_BAD_LENGTH</td>
<td>24</td>
<td>程序发出命令，但命令长度不正确。</td>
</tr>
<tr>
<td>ERROR_SEEK</td>
<td>25</td>
<td>驱动器找不到磁盘上特定区域或磁道。</td>
</tr>
<tr>
<td>ERROR_NOT_DOS_DISK</td>
<td>26</td>
<td>无法访问指定的磁盘或软盘。</td>
</tr>
<tr>
<td>ERROR_SECTOR_NOT_FOUND</td>
<td>27</td>
<td>驱动器找不到请求的扇区。</td>
</tr>
<tr>
<td>ERROR_OUT_OF_PAPER</td>
<td>28</td>
<td>打印机缺纸。</td>
</tr>
<tr>
<td>ERROR_WRITE_FAULT</td>
<td>29</td>
<td>系统无法写入指定的设备。</td>
</tr>
<tr>
<td>ERROR_READ_FAULT</td>
<td>30</td>
<td>系统无法从指定的设备上读取。</td>
</tr>
<tr>
<td>ERROR_GEN_FAILURE</td>
<td>31</td>
<td>连到系统上的设备没有发挥作用。</td>
</tr>
<tr>
<td>ERROR_SHARING_VIOLATION</td>
<td>32</td>
<td>另一个程序正在使用此文件，进程无法访问。</td>
</tr>
<tr>
<td>ERROR_LOCK_VIOLATION</td>
<td>33</td>
<td>另一个程序已锁定文件的一部分，进程无法访问。</td>
</tr>
<tr>
<td>ERROR_WRONG_DISK</td>
<td>34</td>
<td>驱动器中的软盘不对。将 %2 插入(卷序列号: %3)驱动器 %1。</td>
</tr>
<tr>
<td>ERROR_SHARING_BUFFER_EXCEEDED</td>
<td>36</td>
<td>用来共享的打开文件过多。</td>
</tr>
</tbody>
</table>
更多内容见 [windows所有的错误代码](/uploads/winerror.html){:target="_blank"}