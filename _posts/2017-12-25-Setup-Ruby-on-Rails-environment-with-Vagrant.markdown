---
layout: post
title: "Setup Ruby on Rails environment with Vagrant"
date:   2017-12-25 14:00:00
categories: development Rails
tags:
image:
---
https://github.com/rails/rails-dev-box
https://qiita.com/GenTamura84/items/0f97c117df6b1b6e8c0f

## Install Vagrant and VirtualBox
Install Vagrant from [here](https://www.vagrantup.com/downloads.html)
Install VirtualBox from [here](https://www.virtualbox.org/wiki/Downloads)

## Setup Vagrant
Initialize vagrant
{% highlight ruby %}
$ cd your_project_directory
$ vagrant init
{% endhighlight %}
Create blueprint of Virtual machine by `vagrant init` command.
You can define OS, memory etc.
I set it like following.
{% highlight ruby %}
# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64" # Use Ubuntu 14.04
  config.vm.network "forwarded_port", guest: 3000, host: 3000 # Set the port for Rails
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048" # Set memory size
  end
end
{% endhighlight %}

Boot Vagrant
{% highlight ruby %}
vagrant up
vagrant box update # if your current version is old
{% endhighlight %}
log in
{% highlight ruby %}
vagrant ssh
{% endhighlight %}

## Update CentOS packages
{% highlight ruby %}
sudo apt-get -y update
sudo apt-get -y install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties libffi-dev
{% endhighlight %}

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
