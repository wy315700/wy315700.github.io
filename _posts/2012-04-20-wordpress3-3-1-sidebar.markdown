---
layout: post
title:  "WordPress3.3.1 默认主题在文章页面添加侧边栏"
date:   2012-04-20 05:56:12
categories: web
---
最新版 WordPress（v3.3.1）之默认主题 Twenty Eleven 1.3 不再在文章页面提供右侧栏。

我们还是习惯了总是显示右侧栏或在右侧栏放了很多重要东西（如分类、Google AdSense），则这是个非常不人性化的改动。

翻遍 WordPress 外观设置及 Twenty Eleven 1.3 的相关设置，的确找不到相关选项。

怎么办？只有改代码了！

具体步骤如下：

1、在后台点击“Appearance -> Editor”（中文应为“外观 -> 编辑器”），在右侧点击 Single Post （single.php），在最后的 `<?php get_footer(); ?>` 之前加入以下代码：

{% highlight PHP %}
<?php get_sidebar(); ?>
{% endhighlight %}

2、在右侧点击 style.css，注释掉以下样式：

{% highlight css %}
.singular #primary 

{margin: 0;}.singular #content, .left-sidebar.singular #content 

{margin: 0 7.6%;position: relative;width: auto;}

.singular .entry-header, 

.singular .entry-content, 

.singular footer.entry-meta, 

.singular #comments-title {margin: 0 auto;width: 68.9%;}
{% endhighlight %}

3、此时，正文区域大小已正常，如果还需要调整评论区域大小，修改 style.css 里如下样式：

{% highlight css %}
#respond {
    background: #ddd;
    border: 1px solid #d3d3d3;
    -moz-border-radius: 3px;
    border-radius: 3px;
    margin: 0 auto 1.625em;
    padding: 1.625em;
    position: relative;
    width: 68.9%;
}
{% endhighlight %}

把最后的width这一行去掉就行了

保存以后再去文章页面看看，是不是很完美啊

刚刚发现一个问题，提交评论的地方没问题了，但是评论后的地方还是有问题，于是又做了如下修改：

{% highlight css %}
.commentlist .avatar {......left:-102px;...}.commentlist {list-style: none;width: 68.5%}
{% endhighlight %}

修改成

{% highlight css %}
.commentlist .avatar {......left:-90px;...}.commentlist {list-style: none;width: 85%}
{% endhighlight %}
