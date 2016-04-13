---
id: 14
title: Why I am Excited about JRuby
date: 2007-06-21T23:28:48+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/why-i-am-excited-about-jruby/
permalink: /why-i-am-excited-about-jruby/
categories:
  - Software Development
tags:
  - Software Development
---
The <a href="http://jruby.codehaus.org/" target="_blank">JRuby</a>&nbsp;team just released the <a href="http://dist.codehaus.org/jruby/" target="_blank">1.0 version</a>&nbsp;of the library.&nbsp; Having played with Ruby before and Ruby On Rails before, this is an awesome step forward for using Ruby within the enterprise.&nbsp; Now I can build Ruby On Rails applications that can easily interact with other JEE systems and services.&nbsp; I can leverage some of rapid development properties of Ruby while leveraging the benefits of JEE for many server side processes.

As a contrast, at HMS, we have a legacy&nbsp;application that was written in Perl about five years ago&nbsp;that we would like to interact with a JEE application.&nbsp; The application is the classic case of a prototype being built using a scripting language that made its way into production and never left.&nbsp; Now, we are stuck maintaining it, but all of our other systems are in Java and we need to integrate this Perl application with them.

For performance reasons, protocols that involve XML over HTTP (e.g. XML-RPC or SOAP) are out of the question.&nbsp; So our options are pretty limited.&nbsp; We can use Inline::Java and connect over RMI (small community and some interesting JVM issues as we increase the number of Perl processes), we can embed CORBA into our Perl application (I love IDL), or we can write our own wire protocol (all of the pain of CORBA with none of the functionality).

Consider if we had written in JRuby.&nbsp; Since it is running in a JVM, breaking out directly to Java is trivial.&nbsp; So I can easily build out my prototype application and if it turns out that I need to extend the application lifetime, I can easily integrate it into my enterprise architecture.