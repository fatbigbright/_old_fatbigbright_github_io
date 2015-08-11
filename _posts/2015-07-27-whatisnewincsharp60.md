---
layout: post
title: "C# 6.0新特性"
description: ""
category: 
tags: [Dev, C#, .NET]
---
{% include JB/setup %}

##2015年8月11日更新

本文成为了我改善本博客的一个动力和一次契机，当我发现灰白一片的代码片段时，产生了为博客引入代码高亮的特性。

支持代码高亮特性的，是python的一个包pygments，对于整个改动过程，将在另一篇新博文中进行整理。

遗憾的是，到现在为止，pygments对于C#6.0的还支持得不够，读者可以发现，出现用于字符串填写的$符号的代码着色不正常。已向其代码库提交了[issue](https://bitbucket.org/birkenfeld/pygments-main/issues/1138/supporting-c-60)。

##前言

VS2015携C# 6.0已于上周发布，本文根据MSDN上的[视频](https://channel9.msdn.com/Series/Visual-Studio-2015-Enterprise-Videos/Whats-New-in-CSharp-6) ，总结一下C# 6.0语言上的新特性。

根据C#语言项目经理Mads Torgersen在视频中的介绍，C# 6.0语言并非有过多的大变化，而是带来了一些可以让代码表达更为简洁的小变化。变化虽小，却很贴心，让我们逐个来看看吧。

##0. 只包含getter的自动属性与属性初始器（Getter-only auto-properties & Initializer for auto-properties）

从C# 3.0开始，开发人员可以使用自动属性简化类的定义，但如果一个自动属性要实现成只读属性，则必须将setter设置为private，代码类似下述方式：

{% highlight C# %}

public string None { get; private set; }

{% endhighlight%}

在C# 6.0中，只读的自动属性不再需要`private set;`，只写getter即可：

{% highlight C# %}

public string None{ get; }

{% endhighlight%}
    	
这一点变化算是个简化代码的语法糖吧。

另外，C# 6.0 中可以如下方式为一个只读的自动属性设定初始值：

{% highlight C# %}

public string None { get; } = "Nothing";

{% endhighlight%}
    
##1. using关键字的新用法：静态对象

using关键字可以用于引用命名空间，为命名空间定义别名，用于使用继承IDisposable接口的非托管对象等。

C# 6.0中，还可以将这个关键字用于省略静态对象名。

比如每次使用Math静态对象的成员及方法，都要带`Math.`前缀。在C# 6.0，可以在类定义的同级使用`using System.Math;`来省略作用域内所有的该静态对象之下的成员及方法调用时的`Math.`前缀：

{% highlight C# %}

using System.Math;

class Foo
{
    public void DoSomething()
    {
        Console.WriteLine(Sqrt(4));
    }
}

{% endhighlight%}
    
##2. 字符串填写（String Interpolation）

在以前的字符串的拼接中，我们可以使用`string.format("{0}, {1}", foo1, foo2)`或StringBuilder类来减少字符串对象的实例个数。
	
C# 6.0 提供了一种新的填写方式：

{% highlight C# %}

Console.WriteLine($"Sqr(4) equals {Sqr(4)}");

{% endhighlight%}
    	
给一个字符串添加$前缀后，就可以将要包含要填充内容的对象或表达式内嵌在字符串中的{ }中。
	
##3. 表达式为体的属性（Expression-Bodied properties）

其实，这是简化方法的一种方式，它适用于方法体只由返回一个表达式的语句组成。语法上就是将return与大括号换成了类似lambda表达式的方式，比如，一个类方法这样定义：

{% highlight C# %}

public string GetString() { return "Something."; }

{% endhighlight%}

在C# 6.0中，它可以像这样来定义：

{% highlight C# %}

public string GetString() => "Something";

{% endhighlight%}

##4. 索引初始器（Index Initializer）
 
更方便地初始化键-值类型的集合：

{% highlight C# %}

var dic = new Dictionary<string, string> {
    ["love"] = $"{a} loves {b}."
   ,["hate"] = $"{a} hates {b}."
   ,["kill"] = $"{b} wanna kill {a}."
};

{% endhighlight%}

#5. null条件判断操作符

先判断一个类的对象是否为空（null），如果不为空再调用该对象的方法，这样的判断逻辑，我们一定不陌生。

{% highlight C# %}

if(foo != null) 
    foo.someMethod();

{% endhighlight%}

C# 6.0引入了null条件判断操作符（?.）为我们带来了更为简洁地表达这种逻辑的方法：

{% highlight C# %}

foo?.someMethod();

{% endhighlight%}

##6. nameof关键字

用于返回对象或变量的名字。考虑这样一种情况，代码中多处字符串中出现某对象名，而该对象因为某种原因需要重命名，通过IDE的重构功能，可以自动在所有该对象的引用处修改它的名字，但字符串内的对象名则要手动修改或至少要进行文本替换了。

通过nameof关键字，可以将nameof(对象)内嵌到各字符串中，就可免去对象重命名后改名的麻烦：

{% highlight C# %}

Foo anyObj = new Foo();
Console.WriteLine($"name of {nameof(anyObj)}");

{% endhighlight%}
    
##7. 异常过滤器及在异常处理代码块中使用await

C# 6.0允许用户为catch代码块添加过滤器，符合条件的异常才捕获，不符合条件的直接抛到上一级代码来处理：

{% highlight C# %}

try
{
    //CustomizedException is a user-defined class 
    //which inherits Exception class
    throw new CustomizedException("message");
}
catch(Exception ex) when (ex is CustomizedException)
{
    //Do Something
}
    
{% endhighlight%}

在上面的例子中，只有抛出的异常是CustomizedException类的实例，才捕获该异常。

另外，异常处理代码块中可以使用await关键字定义的异步操作了：

{% highlight C# %}

static async void DemoException()
{
    try
    {
        throw new Exception("Anyway");
    }
    catch(Exception e) when (e.Message.Contains("Anyway"))
    {
        await Task.Delay(1000);
        Console.WriteLine("Exception Caught!");
    }
}

{% endhighlight%}
    
以上就是视频中介绍到的C# 6.0中引入的新变化。另将完整的试验代码放到个人的[gist](https://gist.github.com/fatbigbright/655bd7ea2cced7ec66c5)上，请多多指教。