---
id: 107
title: Common Issues and Workarounds While Building EAR files in Eclipse
date: 2009-05-14T20:58:22+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/?p=107
permalink: /common-issues-and-workarounds-while-building-ear-files-in-eclipse/
categories:
  - Software Development
---
In my [Java Programming class](http://www.sgps.psu.edu/cpe/course_descriptions.ashx?linkidentifier=id&itemid=2044) at [Penn State &#8211; Great Valley](http://www.sgps.psu.edu/cpe/default.ashx), we are learning all about Enterprise Java; session beans, JPA, JMS, and all kinds of other Java goodness.&nbsp; In the process, I am teaching them how to build their projects with Eclipse and Maven using the [M2Eclipse](http://m2eclipse.codehaus.org/) plugin.&nbsp; The plugin has improved a lot in the last few months, but we&nbsp; still had to work out the kinks for a couple of issues.

## The Default Maven Install Run Configuration Fails

[This issue](http://jira.codehaus.org/browse/MNGECLIPSE-884) can be seen when running a project that has multiple modules in it. In my case we have an ejb module and then an ear module that is dependent on the ejb module.&nbsp; <a href="http://www.innovationontherun.com/wp-content/uploads/image/DefaultMavenInstall.png" target="_blank"><img width="300" height="157" border="0" align="right" src="http://www.innovationontherun.com/wp-content/uploads/image/DefaultMavenInstall.png" alt="Running Default Maven Install" /></a>When we try to run the default maven install task for the project (see image to the right), we get an error:

> <div>
>   <font face="Courier New">The following mojo encountered an error while executing:</font>
> </div>
> 
> <div>
>   <font face="Courier New"><br /> Group-Id: org.apache.maven.plugins</font>
> </div>
> 
> <div>
>   <font face="Courier New">Artifact-Id: maven-ear-plugin</font>
> </div>
> 
> <div>
>   <font face="Courier New">Version: 2.3.1</font>
> </div>
> 
> <div>
>   <font face="Courier New">Mojo: ear</font>
> </div>
> 
> <div>
>   <font face="Courier New">brought in via: packaging: ear</font>
> </div>
> 
> <div>
>   <font face="Courier New"><br /> While building project:</font>
> </div>
> 
> <div>
>   <p>
>     <font face="Courier New">Group-Id: edu.psu.gv</font>
>   </p>
> </div>
> 
> <div>
>   <font face="Courier New">Artifact-Id: classtwo-ear</font>
> </div>
> 
> <div>
>   <font face="Courier New">Version: 0.0.1-SNAPSHOT</font>
> </div>
> 
> <div>
>   <font face="Courier New">From file: /Users/rdimarco/Documents/workspace/classtwo/ear/pom.xml</font>
> </div>
> 
> <div>
>   <font face="Courier New">Reason: Cannot copy a directory: /Users/rdimarco/Documents/workspace/classtwo/ejb/target/classes; Did you package/install active project artifact:</font>
> </div>
> 
> <div>
>   <font face="Courier New"> artifact = edu.psu.gv:classtwo-ejb:ejb:0.0.1-SNAPSHOT:compile;</font>
> </div>
> 
> <div>
>   <font face="Courier New"> project: MavenProject: edu.psu.gv:classtwo-ejb:0.0.1-SNAPSHOT @ /Users/rdimarco/Documents/workspace/classtwo/ejb/pom.xml?</font>
> </div>

The problem is that there is a bug when the "Resolve Workspace Artifacts" button is checked.&nbsp; To resolve the problem, we need to create a custom run configuration, set the goals to **install**_,_ and make sure the "Resolve Workspace Artifacts" is **unchecked**.

<a href="http://www.innovationontherun.com/wp-content/uploads/image/ChooseRunConfigurations.png" target="_blank"><img width="300" height="157" border="0" align="left" src="http://www.innovationontherun.com/wp-content/uploads/image/ChooseRunConfigurations.png" alt="" /></a>[<img width="301" height="240" border="0" src="http://www.innovationontherun.com/wp-content/uploads/image/MakeSureResolveCheckboxIsUnchecked.png" style="padding-left: 5px;" alt="" />](http://www.innovationontherun.com/wp-content/uploads/image/MakeSureResolveCheckboxIsUnchecked.png)

The build will now proceed correctly

## Unable to Locate the Javac Compiler

Many of my students saw the following error:&nbsp;

> <pre><font size="2" face="Courier New" color="#000000">

<div>
  Reactor Summary:
</div></font></pre>
> 
> <pre><font size="2" face="Courier New" color="#000000">

<div>
  <p>
    [INFO] ------------------------------&lt;wbr>&lt;/wbr>------------------------------&lt;wbr>&lt;/wbr>------------
  </p>
  
  <p>
    [INFO] Class Two ..............................&lt;wbr>&lt;/wbr>............... SUCCESS [1.483s]
  </p>
</div></font></pre>
> 
> <pre><font size="2" face="Courier New" color="#000000">

<div>
  [INFO] ClassTwo EJBs ..............................&lt;wbr>&lt;/wbr>........... FAILED [2.388s]
</div></font></pre>
> 
> <pre><font size="2" face="Courier New" color="#000000">

<div>
  [INFO] ClassTwo EAR ..............................&lt;wbr>&lt;/wbr>............ NOT BUILT
</div></font></pre>
> 
> <pre><font size="2" face="Courier New" color="#000000">

<div>
  [INFO] ------------------------------&lt;wbr>&lt;/wbr>------------------------------&lt;wbr>&lt;/wbr>------------
</div></font></pre>
> 
> <pre><font size="2" face="Courier New" color="#000000">

<div>
  [ERROR]
</div></font></pre>
> 
> <pre><font size="2" face="Courier New" color="#000000">

<div>
  &nbsp;
</div>

<div>
  Mojo: org.apache.maven.plugins:&lt;wbr>&lt;/wbr>maven-compiler-plugin:2.0.2:&lt;wbr>&lt;/wbr>compile
</div>

<div>
  &nbsp;
</div>

<div>
  FAILED for project:<font size="3"> edu.psu.gv:classtwo-ejb:ejb:0.</font>&lt;wbr>&lt;/wbr><font size="3">0.1-SNAPSHOT</font>
</div></font></pre>
> 
> <pre><font size="2" face="Courier New" color="#000000">

<div>
  <font size="3">Reason:</font><font><font size="2" face="Arial" color="#000000"><font size="3" face="Courier New">Unable to locate the Javac Compiler in:<br />&nbsp; C:\Program <br />Files\Java\jre6\..\lib\tools.</font>&lt;wbr>&lt;/wbr><font size="3" face="Courier New">jar<br />Please ensure you are using JDK 1.4 or above <br />and not a JRE (the com.sun.tools.javac.Main class is required).<br />In most cases you can change the location of your Java<br />installation by setting the JAVA_HOME environment variable.</font></font></font>
</div></font></pre>

As the error message suggests, this issue occurs when you are trying to run Maven and your the runtime JRE used to run the Maven executable is from a JRE and not a JDK.&nbsp; To fix this, you need to do the following things:

  1. Install a [valid JDK](http://java.sun.com/javase/downloads/index.jsp) on to your computer.&nbsp; You will need to download the JDK from Sun and then install it on your computer.
  2. Tell Eclipse about the JDK.&nbsp; To do this, you will 
      1. Go to the Preferences.&nbsp; 
          * On Windows, choose the top menu "**Window**" and then select sub-menu "**Preferences"**
          * On a Mac, choose the top menu "**Eclipse**" and then select the sub-menu "**Preferences**"
      2. Click on >Java and then on > Installed JREs
      3. You will see a box listing one or more JREs.&nbsp; Click on the button to the right of this box "**Add**"
      4. You will be given a list of types of JREs to use.&nbsp; Choose **Standard VM**
      5. You will then get a screen asking for the JDK directory.&nbsp; Click on the Directory button and navigate to the newly installed JDK directory 
          * On Windows, this will be something like C:\Program Files\Java\jdk1.6_xx
          * On Mac, this will be something like /System/Library/Frameworks/JavaVM.framework/Versions/1.6.xx/Home
      6. After choosing your directory, Eclipse will think for a bit and then fill in some additional fields on the form.&nbsp; You do not need to make any changes, just hit **Finish**.
  3. Now we will need to tell Maven to use this JDK when it runs 
      1. Right click on your project and choose **Run As -> Run Configurations** (as you normally would to build)
      2. In the Run Configuration dialog, click on the tab **JRE**.
      3. Click on the radio button for **Alternate JRE**
      4. From the drop down list, choose your JDK

You will now be able to run Maven without the error.