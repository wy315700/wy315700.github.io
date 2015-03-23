---
layout: post
title:  "MySQL 5.6 禁用INNODB"
date:   2013-07-01 10:56:12
categories: Database
---
INNODB是MySQL被ORACLE收购后开发的，支持事务和行级锁等高级功能，但是并不是所有人都需要INNODB的，对大部分人来说，以前的MYISAM引擎就够了，一般会选择将默认引擎改为MYISAM，但是INNODB还是会耗费内存和硬盘，这时候，就需要把INNODB彻底禁用。

在以前的MySQL中，一般可以这么设置就行了：

{% highlight Bash shell scripts %}
default-storage-engine=MYISAM
skip-innodb
{% endhighlight %}

但是在最新的MySQL5.6里，这么设置是没法启动的，需要再增加一句设置：


{% highlight Bash shell scripts %}
default-tmp-storage-engine=MYISAM
{% endhighlight %}

不仅如此，还需要添加以下配置，否则程序会很容易退出的：

{% highlight Bash shell scripts %}
loose-innodb-trx=0 
loose-innodb-locks=0 
loose-innodb-lock-waits=0 
loose-innodb-cmp=0 
loose-innodb-cmp-per-index=0
loose-innodb-cmp-per-index-reset=0
loose-innodb-cmp-reset=0 
loose-innodb-cmpmem=0 
loose-innodb-cmpmem-reset=0 
loose-innodb-buffer-page=0 
loose-innodb-buffer-page-lru=0 
loose-innodb-buffer-pool-stats=0 
loose-innodb-metrics=0 
loose-innodb-ft-default-stopword=0 
loose-innodb-ft-inserted=0 
loose-innodb-ft-deleted=0 
loose-innodb-ft-being-deleted=0 
loose-innodb-ft-config=0 
loose-innodb-ft-index-cache=0 
loose-innodb-ft-index-table=0 
loose-innodb-sys-tables=0 
loose-innodb-sys-tablestats=0 
loose-innodb-sys-indexes=0 
loose-innodb-sys-columns=0 
loose-innodb-sys-fields=0 
loose-innodb-sys-foreign=0 
loose-innodb-sys-foreign-cols=0
{% endhighlight %}

摘自http://docs.oracle.com/cd/E17952_01/refman-5.6-en/innodb-turning-off.html