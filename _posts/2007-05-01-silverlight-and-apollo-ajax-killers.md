---
id: 6
title: 'Silverlight and Apollo: AJAX Killers?'
date: 2007-05-01T23:59:00+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/silverlight-and-apollo-ajax-killers/
permalink: /silverlight-and-apollo-ajax-killers/
categories:
  - Software Development
tags:
  - Software Development
  - Technology Business
---
Microsoft just announced the [Silverlight](http://silverlight.net) platform for deploying web applications. [TechCrunch](http://www.techcrunch.com) has a great [summary](http://www.techcrunch.com/2007/04/30/silverlight-the-web-just-got-richer/) of the announcement.&nbsp; Earlier this week, [Adobe](http://www.adobe.com) announced the [Flex](http://www.adobe.com/products/flex/) platform will be [open sourced](http://www.adobe.com/aboutadobe/pressroom/pressreleases/200704/042607Flex.html).&nbsp; This is on top of the speculation of the impact of their [Apollo](http://labs.adobe.com/wiki/index.php/Apollo) platform.&nbsp; Both of these platforms provide developers with new and interesting ways to build out web applications.&nbsp; Both will have wide cross-browser and cross-OS support.

At HMS, we have traditionally been using the HTML/JavaScript/AJAX technology stack for our web GUIs.&nbsp; For a recent development effort that is heavy in data visualizations, one of our developers has been dabbling in Flex, with spectacular results.&nbsp; As a technology manager, I wonder if I should be thinking about moving away from developing any AJAX applications in favor of Flex or Silverlight.

<table width="761" cellspacing="1" cellpadding="1" border="1">
  <caption><font size="5"><strong>AJAX vs. Flex vs. Silverlight Chart</strong></font></caption> <tr>
    <td align="center">
      <strong>Feature</strong>
    </td>
    
    <td align="center">
      <strong>AJAX</strong>
    </td>
    
    <td align="center">
      <strong>Flex</strong>
    </td>
    
    <td align="center">
      <strong>Silverlight</strong>
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
      None required
    </td>
    
    <td valign="top">
      Flash plugin required
    </td>
    
    <td valign="top">
      Silverlight plugin required
    </td>
    
    <td valign="top">
      <p>
        Currently slight advantage for AJAX and Flash.&nbsp;The Flash plugin is ubiquitous and Silverlight is rare, but that will rapidly change.&nbsp; It is also a moot point for internal applications where the browser landscape can be controlled.
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top">
      <strong>Ease of Development for RIAs</strong>
    </td>
    
    <td valign="top">
      <p>
        Mediocre.&nbsp;
      </p>
      
      <p>
        While plenty of decent libraries exist (e.g. Google Web Toolkit , Echo2, et. al.) for helping development effort, there is a severe limitation to what can be done with JavaScript, HTML, and CSS.
      </p>
      
      <p>
        Developers often spend a lot of time writing custom JavaScript to manipulate the DOM.&nbsp; Even leveraging JS libraries like Prototype are clunky.&nbsp; Can do neat things using HTML/CSS hacks, but generally clunky to build anything robust.
      </p>
    </td>
    
    <td valign="top">
      Very good. Well documented libraries.&nbsp; Decent development environment.
    </td>
    
    <td valign="top">
      Remains to be seen, but MS has historically been good at building easy to use development environments.
    </td>
    
    <td valign="top">
      &nbsp;Flex is way ahead of AJAX here
    </td>
  </tr>
  
  <tr>
    <td valign="top">
      <strong>APIs for Visual Display</strong>
    </td>
    
    <td valign="top">
      &nbsp;Poor when compared to other technologies.&nbsp; Limited by HTML/CSS.&nbsp; There are cool and inventive hacks, but nowhere near the functionality available in Flex (and it appears Silverlight)
    </td>
    
    <td valign="top">
      &nbsp;Very Good
    </td>
    
    <td valign="top">
      Seemingly tremendous
    </td>
    
    <td valign="top">
      &nbsp;
    </td>
  </tr>
  
  <tr>
    <td valign="top">
      <strong>Testability</strong>
    </td>
    
    <td valign="top">
      Can be done fairly well when using tools like <a href="http://www.openqa.org">Selenium</a> to build out automated test sets that execute the JavaScript and then check the DOM.
    </td>
    
    <td valign="top">
      Can be painful, especially when trying to
    </td>
    
    <td valign="top">
      &nbsp;Unknown for me.&nbsp; Will the CLR make this easier by allowing for unit tests to be embedded?
    </td>
    
    <td valign="top">
      Testing both AJAX and Flash can be challenging.&nbsp;
    </td>
  </tr>
  
  <tr>
    <td valign="top">
      <strong>Maturity Level</strong>
    </td>
    
    <td valign="top">
      High.&nbsp; JavaScript, HTML, CSS, and XML all are robust, stable, and well understood technologies.
    </td>
    
    <td valign="top">
      Medium.&nbsp; Flex/Flash is a great platform, but I have uncertainty about how the forthcoming Apollo release will affect the technology.&nbsp; This may be more my ignorance than a technology limitation.
    </td>
    
    <td valign="top">
      Low.&nbsp; Beta status
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
      Very large as a general technology, but if you are using a specific library, like Echo 2, the community shrinks significantly.
    </td>
    
    <td valign="top">
      Large
    </td>
    
    <td valign="top">
      Small now, but I imagine it will grow very rapidly
    </td>
    
    <td valign="top">
      &nbsp;
    </td>
  </tr>
</table>

As I look at these technologies, it seems to me that we should really be looking at Flex and Silverlight for more of our internal development.&nbsp; As the technology battle plays out, my bet is that Flex and Silverlight become the top two players in the RIA game, with AJAX falling behind the two.

One thing is for certain, I would not want to be a Java Applet developer.