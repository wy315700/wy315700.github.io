---
layout: post
title:  "取消wordpress自动保存和修订版本"
date:   2012-04-20 08:56:12
categories: web
---
WordPress文章ID不连续的方法的确是一个问题，很让人烦躁，WordPress本身并没有直接的提供关闭这个功能的文章ID不连续的方法选项，那么今天就给大家讲讲如何把这个功能完完全全的隐蔽掉。

### 方法1：

代码来源于国外网站，使用环境：WordPress 3.3.1，原理上 3.0 以上都支持，WP3.0.x 没有进行测试。在我们当前使用主题的 functions.php 文件加入如下代码即可：

{% highlight PHP %}
/* 取消自动保存和修订版本 */

remove_action('pre_post_update', 'wp_save_post_revision');

add_action('wp_print_scripts', 'disable_autosave');

function disable_autosave() {

wp_deregister_script('autosave');

}
{% endhighlight %}

### 方法2：

WordPress默认是每60秒就会对文章进行自动保存，我个人是觉得太频繁了，那么我们可以打开博客根目录下的wp-config.php文件，搜索“require_once(ABSPATH . 'wp-settings.php'）;”在其前面/上面添加如下代码：

{% highlight PHP %}
//自动保存10小时一次

define('AUTOSAVE_INTERVAL', 36000);
//取消自动修订版

define('WP_POST_REVISIONS',false);
{% endhighlight %}

### 清理数据库中以前的文章历史修订版本

自动保存和修订版本我们都解决了，接下来我们进行删除数据库中的冗余文章和修订版本，数据库操作之前大葱建议大家先进行备份。我们登录phpmyadmin 中进行数据库管理，SQL语句命令行中写入以下运行代码执行（如果更改了数据库表名的前缀，需要将数据表名称中wp改成你的前缀）：

{% highlight SQL %}
delete from wp_posts where post_type='revision';
{% endhighlight %}

OK，解决WordPress文章ID不连续的方法大致就这样了。。。