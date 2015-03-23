---
layout: post
title:  "vector容器的capacity和reserve成员"
date:   2012-06-02 10:56:12
categories: Programing
---
我们知道，C++的vector容器可以动态增长，但是为了方便快速的随机读取，vector里的元素是以连续的方式存放的，当我们要往里添加一个元素的时候，就会出现空间不足的情况，这个时候就需要重新分配内存，但是如果每次添加一个元素就要重新分配内存，效率是十分低下的。

vector容器采用了预分配的方法，每次要重新分配内存的时候都会留下一些额外的空间。

vector类提供了两个成员函数：capacity和reserve。其中capacity获取容器真实分配的内存，reserve则告诉容器需要分配多少内存。



先看以下代码：

{% highlight C++ %}
#include<iostream>
#include<vector>
using namespace std;
int main()
{
    vector <int> myvector(100000,10);
    cout<<"my  size :" << myvector.size()<<endl
        <<"my  capacity :"<<myvector.capacity()<<endl;
    return 0;
}
{% endhighlight %}

这段代码创建了一个vector 容器 myvector并初始化为100000个10；

输出是：

{% highlight C++ %}
my  size :100000
my  capacity :100000
{% endhighlight %}

我们可以看到，两个是一样的，因为这个时候并没有重新分配过内存。

添加如下代码后：

{% highlight C++ %}
#include<iostream>
#include<vector>
using namespace std;
int main()
{
    vector <int> myvector(100000,10);
    cout<<"my  size :" << myvector.size()<<endl
        <<"my  capacity :"<<myvector.capacity()<<endl;
    myvector.push_back(100);
    cout<<"my  size :" << myvector.size()<<endl
        <<"my  capacity :"<<myvector.capacity()<<endl;
    return 0;
}
{% endhighlight %}

我们可以看输出变成了：

{% highlight C++ %}
my  size :100000
my  capacity :100000
my  size :100001
my  capacity :200000
{% endhighlight %}

我们可以看到，这里只是往里添加了一个元素，但是myvector对象自动申请了100000个内存空间。

而如果我们用reserve改变预留空间大小的话：

{% highlight C++ %}
#include<iostream>
#include<vector>
using namespace std;
int main()
{
    vector <int> myvector(100000,10);
    cout<<"my  size :" << myvector.size()<<endl
        <<"my  capacity :"<<myvector.capacity()<<endl;
    myvector.reserve(100020);
    myvector.push_back(100);
    cout<<"my  size :" << myvector.size()<<endl
        <<"my  capacity :"<<myvector.capacity()<<endl;
    return 0;
}
{% endhighlight %}

可以看到输出变成了：

{% highlight C++ %}
my  size :100000
my  capacity :100000
my  size :100001
my  capacity :100020
{% endhighlight %}

不过不推荐使用自定义大小，还是让C++自动管理比较好。

当然，还有一个问题，当reserve的设定值比vector要用的小的时候会怎么办呢。把上面代码的reserve(100020)改成reserve(100)以后，输出值又变成：

{% highlight C++ %}
my  size :100000
my  capacity :100000
my  size :100001
my  capacity :200000
{% endhighlight %}

说明当reserve的值比vector大小小的时候，C++会忽略设定的reserve。

 

还有一个问题，初始化的时候是申请了100000个空间，而添加一个元素的时候，是又多申请了100000个空间，不知道有没有联系。