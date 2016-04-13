---
id: 10
title: How Does JavaFX Fit Into the RIA Equation
date: 2007-05-09T18:38:30+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/how-does-javafx-fit-into-the-ria-equation/
permalink: /how-does-javafx-fit-into-the-ria-equation/
categories:
  - Software Development
tags:
  - Software Development
  - Technology Business
---
Sun today announced the release of the [JavaFX](http://www.sun.com/software/javafx/index.jsp) platform.&nbsp; So how does it fit into the matrix.

<table width="761" cellspacing="1" cellpadding="1" border="1">
  <tr>
    <td align="center">
      <strong>Feature</strong>
    </td>
    
    <td align="center">
      <strong>JavaFX</strong>
    </td>
    
    <td align="center">
      <strong>Comments</strong>
    </td>
  </tr>
  
  <tr>
    <td valign="top">
      <strong>Plugin Requirements</strong>
    </td>
    
    <td valign="top">
      JRE required, ~9MB download, but probably 30-40 MB on application startup.
    </td>
    
    <td valign="top">
      <p>
        The JRE has a decent install base, but no where near that of Flash.&nbsp; Most people usingJava WebStart/Applets complains about JRE download and startup time.
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top">
      <strong>Ease of Development for RIAs</strong>
    </td>
    
    <td valign="top">
      Should be comparable to developing Swing applications and allow leveraging of current Java IDEs
    </td>
    
    <td valign="top">
      Java has plenty of good development tools for it
    </td>
  </tr>
  
  <tr>
    <td valign="top">
      <strong>APIs for Visual Display</strong>
    </td>
    
    <td valign="top">
      Not sold on the scripting nature of JavaFX.&nbsp; They probably will need a few releases to make it clean.&nbsp; Based on Swing, an API that few developers are enamored with
    </td>
    
    <td valign="top">
      Java Swing has the feature set, but is generally a pain in implementation.&nbsp; I would rank it last in this category.
    </td>
  </tr>
  
  <tr>
    <td valign="top">
      <strong>Testability</strong>
    </td>
    
    <td valign="top">
      Should be able to leverage Java testing tools
    </td>
    
    <td valign="top">
      &nbsp;
    </td>
  </tr>
  
  <tr>
    <td valign="top">
      <strong>Maturity Level</strong>
    </td>
    
    <td valign="top">
      Java WebStart and applets have been around a long time.
    </td>
    
    <td valign="top">
      &nbsp;
    </td>
  </tr>
  
  <tr>
    <td valign="top">
      <strong>Developer Community</strong>
    </td>
    
    <td valign="top">
      Server side Java is huge, but client side Java has a very small developer community.
    </td>
    
    <td valign="top">
      Due to the problems with Swing, I suspect it will be a tough sell for developers to jump on this bandwagon
    </td>
    
    <td valign="top">
      &nbsp;
    </td>
  </tr>
</table>

&nbsp;

overall, it seems to me that JavaFX is in fourth place RIA technology market.&nbsp; That is not a good place to be, especially when you are competing against two software companies in Adobe and Microsoft with proven track records in developing powerful GUI tools.

As an aside, another difference between the JavaFX platform and the Silverlight is Sun&#8217;s dedication to the mobile platform.&nbsp; Java running on all devices has been key for Sun since the initial releases of Java.&nbsp; However, Sun again makes a branding mistake.&nbsp; We still have the confusion around J2ME, J2SE, J2EE, similar brand names with vastly different purposes.&nbsp; Making the Java FX platform designed for both RIA web apps as well as mobile device GUIs seems like another attempt at confounding the development community.