---
layout: post
title: "The instruction of DI and Dagger2 -part1-"
date:   2017-12-15 14:00:00
categories: development Android
tags:
image: /assets/article_images/2017-12-15-The-instruction-of-DI-and-Dagger2/dagger_bg.jpg
---

# Overview <a name="overview"></a>
In this series, we are going to learn about Dependency Injection by looking at some example and how to use [dagger2](https://github.com/google/dagger) which is commonly used in Android development.

#### About Dependency Injection<a name="about_dependency_injection"></a>
> Dependency injection separates the creation of a client's dependencies from the client's behavior, which allows program designs to be loosely coupled[9] and to follow the dependency inversion and single responsibility principles.[6][10] It directly contrasts with the service locator pattern, which allows clients to know about the system they use to find dependencies.

##### DI Example 1<a name="di_example_1"></a>
First of all, take a look at the following two examples. <a name="di_example_1"></a>
non-DI example:
{% highlight java %}
class Foo {
    private Bar bar;
    private Qux qux;

    public Foo() {
        bar = new Bar();
        qux = new Qux();
    }
}
{% endhighlight %}
You can see class Foo is depending on object bar and qux. Because it create Bar and Qux objects in Foo constructor. This code is not good from the following reasons.

- Hard dependencies reduce the reusability
- Hard dependencies make it hard for testing
- Hard dependencies hinders code maintainability when the project scales up


Here is what improved the above code.
DI example:
{% highlight java %}
class Foo {
    private Bar bar;
    private Qux qux;

    public Foo(Bar bar, Qux qux) {
        this.bar = bar;
        this.qux = qux;
    }
}

// in production:
new Foo(new Bar(), new Qux());
// in test:
new Foo(new BarMock(), new Qux());
{% endhighlight %}
You can inject Bar and Qux objects from the outside of the Foo class. ✨

##### DI Example 2<a name="di_example_2"></a>
{% highlight java %}
class Blogger {
    public Blogger() {
    }

    public void post(String title) {
        FileLogger logger = new FileLogger(title);
        logger.logging();
    }
}

class FileLogger implements Logger {
    @Override
    public void logging(String... messages) {
        File file = new File("hoge.txt");
    }
}

interface Logger {
    void logging(String messgae);
}
{% endhighlight %}
Why this code is not good?🤔
Now you can see that class Blogger is depending on FileLogger.

{% highlight java %}
class Blogger {
    Logger logger;

    public Blogger(Logger logger) {
        this.logger = logger;
    }

    public void post(String title) {
        logger.logging();
    }
}
{% endhighlight %}
By doing this, you can inject some class which implement Logger interface from the out of the Blogger class. The reason why doing this is good is you can change the way to take log without changing the Blogger class!


#### Conclusion<a name="conclusion"></a>
In this post we learned about DI by using examples. In part 2 we will actually learn examples of using dagger in Android development project.

###### Ref
- [Understanding dependency injection](https://softwareengineering.stackexchange.com/questions/232229/understanding-dependency-injection)

- [Dagger 2 for Android Beginners — Introduction](https://medium.com/@harivigneshjayapalan/dagger-2-for-android-beginners-introduction-be6580cb3edb)

https://rakuishi.com/archives/di-dagger2/
https://qiita.com/kiimiiis/items/20c3fa79988ed3ccb3c9
http://satoshun.github.io/2015/05/dagger2/
http://starzero.hatenablog.com/entry/2016/12/02/084412

Have a good hacking day!👋

If you are Android developer or learning Android development, follow me on [Twitter](https://twitter.com/Taishi_Yamasaki)!🐦
