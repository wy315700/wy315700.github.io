---
layout: post
title:  "Mac下安装使用pypy"
date:   2015-03-29 23:25:12
categories: Mac
---

PyPy是一个比CPython更好的解释器。
在Mac上测试使用了一下。

### 安装

如果你有 `homebrew` 的话，安装过程会非常简单

{% highlight Bash shell scripts %}
$ brew install pypy
{% endhighlight %}

这样就会自动搜索安装了

### 使用

在命令行里使用 `pypy` 代替 `python` 就可以了。

### pip

当然忘不了伟大的 `pip` 方法也很简单。

brew安装以后会自动生成以下两个文件：

* easy_install_pypy
* pip_pypy

使用两个代替 `pip` 或者 `easy_install` 就可以了。

是不是很简单啊

### 测试

对我目前的一个项目进行了一下测试

#### cPython 

{% highlight Bash shell scripts %}
$ ab -n 1000 -c 20 "http://127.0.0.1:8000/v0.2/posts?page=1&perPage=15"
This is ApacheBench, Version 2.3 <$Revision: 1554214 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 127.0.0.1 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests


Server Software:        TornadoServer/4.1
Server Hostname:        127.0.0.1
Server Port:            8000

Document Path:          /v0.2/posts?page=1&perPage=15
Document Length:        14127 bytes

Concurrency Level:      20
Time taken for tests:   16.366 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      14493000 bytes
HTML transferred:       14127000 bytes
Requests per second:    61.10 [#/sec] (mean)
Time per request:       327.311 [ms] (mean)
Time per request:       16.366 [ms] (mean, across all concurrent requests)
Transfer rate:          864.82 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.2      0       2
Processing:    87  324  32.6    317     453
Waiting:       87  324  32.6    317     453
Total:         89  324  32.5    317     453

Percentage of the requests served within a certain time (ms)
  50%    317
  66%    328
  75%    332
  80%    336
  90%    359
  95%    368
  98%    434
  99%    444
 100%    453 (longest request)
 {% endhighlight %}

内存占有率是 40M

#### pypy

{% highlight Bash shell scripts %}
$ ab -n 1000 -c 20 "http://127.0.0.1:8000/v0.2/posts?page=1&perPage=15"
This is ApacheBench, Version 2.3 <$Revision: 1554214 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 127.0.0.1 (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests


Server Software:        TornadoServer/4.1
Server Hostname:        127.0.0.1
Server Port:            8000

Document Path:          /v0.2/posts?page=1&perPage=15
Document Length:        14127 bytes

Concurrency Level:      20
Time taken for tests:   27.627 seconds
Complete requests:      1000
Failed requests:        0
Total transferred:      14493000 bytes
HTML transferred:       14127000 bytes
Requests per second:    36.20 [#/sec] (mean)
Time per request:       552.548 [ms] (mean)
Time per request:       27.627 [ms] (mean, across all concurrent requests)
Transfer rate:          512.29 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.1      0       2
Processing:    27  548 266.5    516    8026
Waiting:       27  547 266.5    516    8026
Total:         28  548 266.5    516    8027

Percentage of the requests served within a certain time (ms)
  50%    516
  66%    552
  75%    572
  80%    587
  90%    614
  95%    672
  98%   1104
  99%   1245
 100%   8027 (longest request)
 {% endhighlight %}

内存占用飙到了 240M

而且性能不是很理想，难道是我的使用方法不对，等过段时间再进行研究吧。
