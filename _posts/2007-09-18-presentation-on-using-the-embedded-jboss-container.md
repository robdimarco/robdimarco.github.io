---
id: 40
title: Presentation on Using the Embedded JBoss Container
date: 2007-09-18T21:37:52+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/presentation-on-using-the-embedded-jboss-container/
permalink: /presentation-on-using-the-embedded-jboss-container/
categories:
  - Software Development
tags:
  - Java
  - Open Source
  - Software Development
---
Gave a presentation tonight to the [Philly JBoss Users Group](http://www.pjbug.com) about using [Embedded JBoss](http://wiki.jboss.org/wiki/Wiki.jsp?page=EmbeddedJBoss). 

For the unaware, Embedded JBoss allows a developer to embed much of the functionality of the JBoss application server (EJB, Messaging, JNDI, Security, Transactions) in another application.&nbsp; A great use for the technology is in the creation of unit tests that can actually test Session, Entity, and Message Driven Beans.&nbsp; At [Health Market Science](http://www.healthmarketscience.com), we heavily used the Embedded EJB container (a predecessor to Embedded JBoss) connecting to a in-memory Hypersonic DB for improving out unit tests.&nbsp; It made developing and debugging significantly easier and all of the developers loved doing it.&nbsp; I highly encourage any developer that is developing using EJB3, and especially anyone using JBoss, to use the combination of Embedded JBoss.

Full Presentation can be found at [http://www.innovationontherun.com/presentation-files/Intro%20to%20Embedded%20JBoss.pdf](http://www.innovationontherun.com/presentation-files/Intro%20to%20Embedded%20JBoss.pdf "http://www.innovationontherun.com/presentation-files/Intro%20to%20Embedded%20JBoss.pdf")