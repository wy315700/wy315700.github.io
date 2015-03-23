---
layout: post
title:  "在wordpress里使用独立的Description 和 Keywords"
date:   2012-12-14 10:56:12
categories: web
---
大多数wordpress主题对于keywords和description这两个meta标签一般都做得很差，或者根本就不提供，这样不利于SEO。这不博主利用 google webmaster 检查网站的时候发现谷歌提示说:

包含重复的元说明

![20121214160740]({{site_url}}/uploads/2012/12/20121214160740.png)

还是得自定义这两个字段啊，于是上网搜索代码，最后是在露兜博客找了段代码


{% highlight PHP %}
<?php
if (is_home() || is_page()) {
    // 将以下引号中的内容改成你的主页description
    $description = "天外之音的博客";

    // 将以下引号中的内容改成你的主页keywords
    $keywords = "天外之家,千千世界,静静倾听,天外之音,博客,编程,php,linux";
}
elseif (is_single()) {
    $description1 = get_post_meta($post->ID, "description", true);
    $description2 = the_title('','',false);

    // 填写自定义字段description时显示自定义字段的内容，否则使用文章内容前200字作为描述
    $description = $description1 ? $description1 : $description2;
    $description.=",天外之音的博客";
    // 填写自定义字段keywords时显示自定义字段的内容，否则使用文章tags作为关键词
    $keywords = get_post_meta($post->ID, "keywords", true);
    if($keywords == '') {
        $tags = wp_get_post_tags($post->ID);    
        foreach ($tags as $tag ) {        
            $keywords = $keywords . $tag->name . ",";    
        }
        $keywords = rtrim($keywords, ', ');
    }
$keywords .= ",天外之家,千千世界,静静倾听,天外之音,博客,编程";
}
elseif (is_category()) {
    $description = category_description();
    $keywords = single_cat_title('', false);
    $description .=",天外之音的博客";
    $keywords .= ",天外之家,千千世界,静静倾听,天外之音,博客,编程";
}
elseif (is_tag()){
    $description = single_tag_title('', false);
    $keywords = single_tag_title('', false);
$description+=",天外之音的博客";
$keywords += +",天外之家,千千世界,静静倾听,天外之音,博客,编程";
}
$description = trim(strip_tags($description));
$keywords = trim(strip_tags($keywords));
?>
<meta name="description" content="<?php echo $description; ?>" />
<meta name="keywords" content="<?php echo $keywords; ?>" />
{% endhighlight %}

以后在写博客文章时只需添加两个自定义字段（在文章编辑页面下面）即可，第一个自定义字段名称为keywords，字段值写上这篇文章的关键字。接着再添加第二个自定义字段，自定义字段名称为description，后面的字段值写上这篇日志的描述。自定义字段用过一次后，以后再写日志只需在下拉框中选择即可。这样每篇文章都有你自定义的keywords和description了。

这样一来，每个页面的关键词和描述也就更好了。