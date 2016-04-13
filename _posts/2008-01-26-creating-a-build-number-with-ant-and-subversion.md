---
id: 45
title: Creating a Build Number With Ant and Subversion
date: 2008-01-26T00:44:25+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/creating-a-build-number-with-ant-and-subversion/
permalink: /creating-a-build-number-with-ant-and-subversion/
categories:
  - Software Development
tags:
  - Java
  - Software Development
---
I&#8217;m on a hot streak tonight&#8230; Another comon problem that I have had to solve recently is how to attach a build number to files that are being generated out of an [Ant](http://ant.apache.org) build script.

<p align="left">
  We have developed a versioning scheme (inspired by http://geekswithblogs.net/emanish/archive/2006/09/25/92219.aspx) for our releases where our release are named <major>.<minor>.<revision>.<build>. So a valid build number might be 1.18.2.1077. This means it belongs to the first major release of the software, the eighteenth minor version, it is the second attempt at a release for this minor version (usually bug fixes), and it is related to the SVN revision number 1077. Any time we are doing a build we are tagging in our Subversion repository to keep the build number tied to the tag. When I build a release, I want my artifacts to look like to look like this: app-1.18.2.1077.ear.
</p>

<p align="left">
  There are a few ways to do this, One way that I considered was to just include a property or Subversion tag in the build.properties file. But I did not want to maintain it and I have always loathed the auto tags that were available in CVS.
</p>

<p align="left">
  My second option was to use the <svn> Ant task. While this will probably work, I have varying degrees of success using the Java SVN library, especially when connecting to Subversion over SSH as we are doing for this application.
</p>

<p align="left">
  So, I decided to go with using the task in conjunction with <em> svn info</em> and <em>svnversion</em> to set the full.build.version property to the current release number. This assumes that the repository checked out is of the format <em>svn://REPO_NAME/tags/<MAJOR>.<MINOR>.<REVISION></p> 
  
  <pre> 
&lt;exec outputproperty="build.current.revision" executable="svnversion"&gt;
         &lt;arg line="-n -c" /&gt;
         &lt;redirector&gt;
           &lt;outputfilterchain&gt;
             &lt;tokenfilter&gt;
                 &lt;replaceregex pattern="^[0-9]*:?" replace="" flags="g"/&gt;
             &lt;/tokenfilter&gt;
          &lt;/outputfilterchain&gt;
        &lt;/redirector>
&lt;/exec&gt;
&lt;exec outputproperty="build.current.version" executable="svn"&gt;
  &lt;arg line="info" /&gt;
  &lt;redirector&gt;
  &lt;outputfilterchain&gt;
  &lt;linecontainsregexp&gt;&lt;regexp pattern="^URL:" /&gt;&lt;/linecontainsregexp&gt;
   &lt;tokenfilter&gt;
     &lt;replaceregex pattern=".*\/([^\/]+)$" replace="\1" flags="g"/&gt;
   &lt;/tokenfilter&gt;
  &lt;/outputfilterchain&gt;
&lt;/redirector&gt;
&lt;/exec&gt;

&lt;property name="full.build.version" value="${build.current.version}.${build.current.revision}" /&gt;</pre>
  
  <p>
    I&#8217;m sure that I could have gotten the revision from the svn info rather than svnversion, saving a command, but I wasn&#8217;t inspired enough to do it. Maybe when I have more time.
  </p>