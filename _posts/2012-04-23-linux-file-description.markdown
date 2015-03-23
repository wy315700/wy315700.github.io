---
layout: post
title:  "Linux文件描述符"
date:   2012-04-23 10:56:12
categories: Linux
---
在Linux内核里，每个打开的文件都有一个对应的文件描述符。每个文件描述符是一个非负整数，当程序打开一个文件的时候，内核就把对应的文件描述符发给这个进程。而程序就通过这个文件描述符来识别不同的文件。

按照惯例，0被用来描述标准输入，1是标准输出，2是标准错误输出。

在程序里，最好用STDIN_FILENO,STDOUT_FILENO,STDERR_FILENO

取代这三个数，这三个常数在<unistd.h>里定义。

文件描述符的最大值是 OPEN_MAX；

