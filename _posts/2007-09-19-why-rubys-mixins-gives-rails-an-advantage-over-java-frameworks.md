---
id: 41
title: 'Why Ruby&#8217;s Mixins Gives Rails an Advantage over Java Frameworks'
date: 2007-09-19T18:57:42+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/why-rubys-mixins-gives-rails-an-advantage-over-java-frameworks/
permalink: /why-rubys-mixins-gives-rails-an-advantage-over-java-frameworks/
tags:
  - Java
  - ruby
---
Much has been made over [Paul Graham&#8217;s](http://www.paulgraham.com)&nbsp;famous&nbsp;posting about how [Lisp](http://en.wikipedia.org/wiki/Lisp_programming_language)&nbsp;[gave his startup Viaweb an advantage](http://www.paulgraham.com/avg.html) over the competition.&nbsp; Graham&#8217;s thesis is that there are features in the Lisp language that could be leveraged to make his programming team more productive and better able to respond to customer needs.

The idea that a programming language will make your team more productive is the Holy Grail of software development.&nbsp; Many languages have been promoted as a cure all for productivity (C++ in the 80s, Java in the 90s, Ruby now, Lisp perpetually), and each turns out to have their benefits and weaknesses.&nbsp; But there is no doubt that certain features in languages can lead to leaps in improvements in productivity.&nbsp; For example, having built in garbage collection&nbsp;is a _sine qua non_ for modern development languages.&nbsp; No one can argue (at least no one can argue well) that garbage collection does not improve developer productivity for 99.9% of development efforts.

## What is Mixin?

Since many readers may not be aware of what a mixin is, I will try to describe it briefly.&nbsp; Ruby is an object-oriented language.&nbsp; With all object-oriented languages, the designers had to decide whether or not to support [multiple inheritance](http://en.wikipedia.org/wiki/Multiple_inheritance#Debate).&nbsp; Perl and C++&nbsp;designers allow for multiple inheritance, Java does not allow for multiple inheritance but does allow for polymorphism through the use of interfaces.&nbsp; Ruby&#8217;s designers&nbsp;opted to not allow multiple inheritance, but to allow for code from one module to be able to be included in another class.

So consider the following code:

<div style="border-right: 1px dotted; border-top: 1px dotted; padding-left: 1em; border-left: 1px dotted; border-bottom: 1px dotted">
  <pre>module BarModule
  def hello_world
    puts "Hello World"
  end
end

class BaseClass
  def class_method
    puts "In class method"
  end
end

class Foo &lt; BaseClass
  include BarModule
end

f = Foo.new
f.class_method
f.hello_world
</pre>
</div>

&nbsp;In this example, we are creating a module named _BarModule_&nbsp;with a _hello_world_ method, a class named _BaseClass_, and then another class _Foo_ that extends _BaseClass_ and includes _BarModule_. The class _Foo_ will then have both the methods from _BarModule_ and _BaseClass_, but it will only _BE_ an instance of _BaseClass_ as that is its only parent. Somewhat different than what a Java developer would do, but it makes sense. Running this file will result in the following output:

<div style="border-right: 1px dotted; border-top: 1px dotted; padding-left: 1em; border-left: 1px dotted; border-bottom: 1px dotted">
  <pre>$ ruby foo.rb
In class method
Hello World
$
</pre>
</div>

So that is a basic example of what a mixin is.

## Adding send() into the equation

Another interesting and powerful thing about Ruby is that all method calls are actually message passing calls.&nbsp; So for example, we could rewrite:

<div style="border-right: 1px dotted; border-top: 1px dotted; padding-left: 1em; border-left: 1px dotted; border-bottom: 1px dotted">
  <pre>f = Foo.new
f.class_method
f.hello_world
</pre>
</div>

to

<div style="border-right: 1px dotted; border-top: 1px dotted; padding-left: 1em; border-left: 1px dotted; border-bottom: 1px dotted">
  <pre>f = Foo.send(:new)
f.send(:class_method)
f.send(:hello_world)
</pre>
</div>

and the results would be exactly the same! Again a little weird if new to Ruby, but it this language feature can lead to some very interesting and powerful results.

## Combining include and send

Now let&#8217;s combine these two methods.&nbsp; What if I wanted to take my _BarModule_ and apply it to a class that is not sign, say the ruby base class _String_.

<div style="border-right: 1px dotted; border-top: 1px dotted; padding-left: 1em; border-left: 1px dotted; border-bottom: 1px dotted">
  <pre>String.send(:include, BarModule)
s = "Arbitrary String"
s.hello_world
</pre>
</div>

Running the above code would produce: 

<div style="border-right: 1px dotted; border-top: 1px dotted; padding-left: 1em; border-left: 1px dotted; border-bottom: 1px dotted">
  <pre>$ ruby include-bar-module-on-string.rb
Hello World
$
</pre>
</div>

That&#8217;s right, at runtime, I was able to add an arbitrary method onto the base _String_ class. That method is now available to any _String_s that I instantiate within my application. 

## The Implication of this Feature on Rails

Because of this mixin feature, a developer can add arbitrary methods and modify behavior of core classes at runtime.&nbsp; This is amazingly powerful if you are trying to write plugins and extensions to the framework.&nbsp; Because you can add functionality to existing objects, users can install your plugin and start taking advantage of new functionality without having to make changes to the objects that they are instantiating in their application.&nbsp; In frameworks written in other languages, such as Java, plugging in new functionality means that you need to change how your objects are instantiated.&nbsp; This will require code changes and/or potentially configuration changes (if you are using a dependency injection framework like Spring).&nbsp; Hard to develop, hard&nbsp;to maintain, and a pain for plugin developers to support.&nbsp; But because of the mixin feature, Rails plugin developers can customize the base objects and the users of the plugins do not have to change any of their code or configuration logic.&nbsp; A good example of this can be seen in the [Row Version](http://www.redhillonrails.org/#row_version_migrations)&nbsp;Rails plugin.&nbsp; This particular plugin puts a **created\_at, UPDATED\_AT**&nbsp;and a&nbsp;**row_version** on every row inserted into the database.&nbsp; It requires ZERO code change to make this happen.&nbsp; You just install the plugin and go.&nbsp; It works by adding hooks into the _ActiveRecord::Base_ (the base persistence class in Rails) so that when records are saved, the correct information is put in to those fields.&nbsp; A very easy and powerful plugin to install and use.

## Conclusions

The point to take away from this is not that Ruby on Rails rocks and Java sucks.&nbsp; Far from it.&nbsp; But choosing a framework with lots of extensions that can take care of many of the mundane tasks allows your developers to spend more of their time focused on the problems of the user and not on common problems.&nbsp; The mixin feature of Ruby allows for the development of easy to use but powerful plugins that will be hard for any non-Ruby based framework to compete with.