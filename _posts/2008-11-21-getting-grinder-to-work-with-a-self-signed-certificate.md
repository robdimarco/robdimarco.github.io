---
id: 80
title: Getting Grinder To Work with a Self-Signed Certificate
date: 2008-11-21T19:45:54+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/?p=80
permalink: /getting-grinder-to-work-with-a-self-signed-certificate/
title_tag:
  - ' Getting Grinder To Work with a Self-Signed Certificate'
categories:
  - Java
  - Software Development
tags:
  - grinder java ssl jsse
  - https
---
Recently, I&#8217;ve been working a bunch with [Grinder](http://grinder.sourceforge.net/index.html) to do some load testing.&nbsp; I&#8217;ve had great success with it in the past, and wanted to punish an app.&nbsp; My test needs to make HTTP and HTTPS requests which I never anticipated would be a problem.&nbsp; Unfortunately, my server has a self-signed certificate which the Java processes refused to recognize.&nbsp; I tried adding the certificates through the Java Console but that led to these weird no peer exceptions (shudder).

I figured that I would have to use keytool to load the certificates.&nbsp; What I did not realize is that [you cannot load a self-signed key where you already have a certificate using keytool](http://www.agentbob.info/agentbob/79-AB.html) directly!&nbsp; So I had to follow the following steps

  1. Convert my certs from PEM format into DER format

<pre><span class="Code">&gt; openssl pkcs8 -topk8 -nocrypt -in server.key \</span></pre>

<pre><span class="Code">-inform PEM -out key.der -outform DER<br /> &gt; openssl x509 -in server.csr -inform PEM \</span></pre>

<pre><span class="Code">-out cert.der -outform DER</span></pre>

<ol type="1" start="2">
  <li>
    Use the <a href="http://www.agentbob.info/agentbob/80.html">Java code from this AgentBob post</a> to create a keystore
  </li>
</ol>

<span class="Code">> java -Dkeystore=mycerts ImportKey key.der cert.der</span>

<ol type="1" start="3">
  <li>
    Now when running the <a href="http://grinder.sourceforge.net/g3/tcpproxy.html">TCPProxy</a>, I had to add the following:
  </li>
</ol>

<span class="Code">java -Djavax.net.debug=all -classpath $GRINDER_JAR \</span>

<span class="Code">net.grinder.TCPProxy -console -http \</span>

<span class="Code">-keystore mycerts -keyStorePassword importkey</span>

<ol type="1" start="4">
  <li>
    To use the certs from my <a href="http://grinder.sourceforge.net/g3/agents-and-workers.html">Agent process</a>
  </li>
</ol>

<pre><span class="Code"><br /><br />from java.lang import System<br />grinder.SSLControl.setKeyStoreFile(System.getProperty("keystore"),System.getProperty("keypass"))<br /></span></pre>

<ol type="1" start="4">
  <li>
    Finally, I needed to add this to the properties file for my Grinder agent process
  </li>
</ol>

<span class="Code">grinder.jvm.arguments=-Dkeystore=mycerts -Dkeypass=importkey</span>

And tada it works! One other tidbit: using <span class="Code">-Djavax.net.debug=ssl</span> was invaluable in debugging. You can use <span class="Code">-Djavax.net.debug=help</span> to find out all of the debug options.