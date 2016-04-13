---
id: 44
title: Integrating Cached Content with Apache and JBoss
date: 2008-01-26T00:25:50+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/integrating-cached-content-with-apache-and-jboss/
permalink: /integrating-cached-content-with-apache-and-jboss/
categories:
  - Software Development
tags:
  - Software Development
---
<p align="left">
  One of my clients is a large e-commerce company (>$500MMin annual revenuse through their website). They use Akamai for caching some of their image data, but some of the other images are served off of their server. They are trying to minimize the load on their JBoss instances and wanted to have Apache serve the static content when possible while JBoss handled the dynamic content. Both Apache and JBoss were running on the same Linux server.
</p>

<p align="left">
  This is a problem that I have faced a couple of times and every time I spend a few hours futzing with Apache configurations trying to get this to work. So I figure I should post the basic solution and never have to look hard for it again. Also, if someone has better suggestions, let me know.
</p>

<p align="left">
  <h1>
    Step 1: Activate the Requisite Modules
  </h1>
  
  <p align="left">
    I use four modules for getting this to work
  </p>
  
  <ul>
    <li>
      <a href="http://httpd.apache.org/docs/2.0/mod/mod_alias.html">mod_alias</a>
    </li>
    <li>
      <a href="http://httpd.apache.org/docs/2.0/mod/mod_proxy.html">mod_proxy</a>
    </li>
    <li>
      <a href="http://httpd.apache.org/docs/2.0/mod/mod_proxy_http.html">mod_proxy_http</a>
    </li>
    <li>
      <a href="http://httpd.apache.org/docs/2.0/mod/mod_rewrite.html">mod_rewrite</a>
    </li>
  </ul>
  
  <p>
    Make sure these are activated in your configuration<br /> 
    
    <h1>
      Step 2: Setting Up The Static Content
    </h1>
    
    <p align="left">
      First step is to set up a directory location that will ahve the content. In my example, I am dealing with an exploded EAR in the Jboss home directory. This results in a configuration like
    </p>
    
    <pre>
# 
# Configure the static content directory and alias.  This is pointing at the cache 
# file directory 
# 

Alias /static-content/ "/usr/local/jboss/cache/app" 

# 
# Do not allow any overriding from within the cache directory. # 
&lt;directory /usr/local/jboss/cache/app" &gt;
  Options None
  AllowOverride None
  Order allow,deny
  Allow from all 
&lt;/directory&gt;
</pre>
    
    <p align="left">
      Step 3: Set up the rewrite rules
    </p>
    
    <p align="left">
      <pre> 
# 
#  Rewrite all static content  to be served from the 
#  static content directory 
# 
&lt;IfModule mod_rewrite.c&lt;
    RewriteEngine on 

    #
    # Setup the static content rule.  Any file that comes in that# we have on the filesystem, we should serve from there.
    #
    # We want this to be the last rule processed if we meet the conditions
    # and we need to pass through so that the static-content alias works.
    #
    RewriteCond /usr/local/jboss/cache/app/%{REQUEST_FILENAME} -f
    RewriteRule ^/?(.*)$ /static-content/$1 [L,PT] 
&lt;/IfModule&gt; 

&lt;IfModule mod_proxy.c&gt;

# No need to forward proxy
ProxyRequests Off 

# Make sure we clean up the headers
# you may also wish to use the mod_proxy_ajp to proxy
# over the ajp protocol

  ProxyPassReverse / http://localhost:8080/ 

# Needed to maintain proper host information
  ProxyPreserveHost On 
&lt;/IfModule&gt; </pre>