---
layout: post
title:  "Rails强制开启SSL"
date:   2014-08-09 10:56:12
categories: web
---
### Rails >= 3.1

如果你的Rails版本大于3.1,那么就非常的简单，只需要使用 `config.force_ssl = true` 即可，具体代码如下：

{% highlight ruby %}
# config/application.rb
module MyApp
  class Application < Rails::Application
    config.force_ssl = true
  end
end
{% endhighlight %}

当然你也可以设置不同的环境，比如在开发环境关闭SSL，在生产环境打开，可以使用以下方法：

{% highlight ruby %}
# config/application.rb
module MyApp
  class Application < Rails::Application
    config.force_ssl = false
  end
end

# config/environments/production.rb
MyApp::Application.configure do
  config.force_ssl = true
end
{% endhighlight %}

默认情况下， Rails使用Rack::SSL Rack middleware 来强制使用SSL



### Rails < 3.1

如果你的Rails版本小余3.1，也不用担心，可以手动打开SSL支持，把如下代码加入到环境配置里

{% highlight ruby %}
config.middleware.insert_before ActionDispatch::Static, "Rack::SSL"
{% endhighlight %}

同时别忘了在Gemfile里加入rack-ssl的支持

{% highlight ruby %}
# Gemfile

gem 'rack-ssl', :require => 'rack/ssl'
{% endhighlight %}

### 同时使用HTTPS和HTTP

以上方法是把全部访问强制重定向到HTTPS，当然，我们也可以并列使用HTTPS和HTTP，Rack::SSL拥有大量没有在文档里的内容，其中一个东西就是:exclude选项，我们可以在环境配置里加入以下配置：

{% highlight ruby %}
config.middleware.insert_before ActionDispatch::Static, Rack::SSL, :exclude => proc { |env| env['HTTPS'] != 'on' }
{% endhighlight %}

使用该配置以后，如果你使用HTTPS访问网站，它会把所有的资源链接换成HTTPS，当然如果使用HTTP访问，那么什么都不会有变化。