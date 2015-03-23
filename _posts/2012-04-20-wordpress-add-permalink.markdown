---
layout: post
title:  "WordPress 不用插件给每篇文章都添加原文地址"
date:   2012-04-20 06:56:12
categories: web
---
问：如何让转载自己博客的文章的朋友更加方便的在原文中加入原文的地址呢？

答：那就是在原文中留下这些信息，具体操作如下

1. 在主题模板中找到single.php文件，
2. 在合适的地方加入下面的代码:



{% highlight PHP %}
<p>原文来自：<a href=”<?php echo home_url(‘/’);?>”><?php bloginfo(‘name’);?></a>. 本文链接地址：<a href=<?php the_permalink();?>><?php the_title();?></a>
{% endhighlight %}

好了，更新一下你的页面看一下吧！

