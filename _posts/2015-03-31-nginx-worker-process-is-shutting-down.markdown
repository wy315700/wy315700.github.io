---
layout: post
title:  "nginx: worker process is shutting down 原因解析"
date:   2015-03-31 17:00:12
categories: web
---

今天配置服务器的时候，发现了很多的 `nginx: worker process is shutting down` 。

![nginx: worker process is shutting down](/uploads/2015/03/QQ20150331-1.png)

顿时整个人都警觉起来了，是不是服务出问题了。

后来查资料后发现，这个完全不是问题，出现这个的原因是因为我重启 `nginx` 的时候用了 `service nginx reload` ，这是一种平滑重启的方案，不会把已有的连接断掉，而这些维护已有连接的 `nginx` 进程则会进入 `nginx: worker process is shutting down` 状态，稍微等一等就好了。

果然，过了10分钟， `nginx: worker process is shutting down` 进行就都消失了。
