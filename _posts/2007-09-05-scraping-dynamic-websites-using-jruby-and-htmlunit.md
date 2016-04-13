---
id: 38
title: Scraping Dynamic Websites Using JRuby and HtmlUnit
date: 2007-09-05T16:58:21+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/scraping-dynamic-websites-using-jruby-and-htmlunit/
permalink: /scraping-dynamic-websites-using-jruby-and-htmlunit/
tags:
  - Java
  - ruby
---
Scraping static web sites to verify functionality or to access data has been around as long as there has been a web (example of [scraping of a static web&nbsp;page with Ruby](http://muharem.wordpress.com/2007/09/04/scrape-the-web-with-ruby/)).&nbsp; But with the advent of AJAX and other techniques that use JavaScript to dynamically insert HTML into a web page, scraping has gotten more challenging.&nbsp; Most scraping technology does fine when downloading a single HTML page, but cannot easily handle the dynamic content.

With the 1.12 release of [HtmlUnit](http://htmlunit.sourceforge.net), this&nbsp;headless web browser can now support parsing and executing JavaScript.&nbsp; This allows a scraper to access this dynamic content as simply as the scraper accesses static content, and without having to fire up a heavy execution engine like Gecko.

[JRuby](http://jruby.codehaus.org) is a great technology for easily construct a script that calls into the HtmlUnit functionality without having to deal with all the syntactic sugar that Java requires.</p> 

# Step 0: About The Example Code

The tar ball, [scraper.tgz](http://www.innovationontherun.com/jruby-scraper/scraper.tgz), contains:

  * [scraper.rb](http://www.innovationontherun.com/jruby-scraper/scraper.rb) &#8211; the JRuby script we will be executing.&nbsp; All code discussed in this example comes from there 
      * lib/*.jar &#8211; all of the JAR files needed to run the example 
          * run.sh &#8211; a simple shell script that&nbsp;points JRuby at the lib directory and silences some warning messages</ul> 
        # Step 1: Enabling JRuby to Use the Java JAR files
        
        <table cellspacing="0" cellpadding="0" width="451" border="0" unselectable="on">
          <tr>
            <td valign="top" width="449">
              <pre class="code"># Require Java so we can use the Java libraries
require 'java';

# Get HTML Unit and all of its required libraries
require 'htmlunit-1.13.jar';
require 'commons-httpclient-3.1.jar';
require 'commons-io-1.3.1.jar';
require 'commons-logging-1.1.jar';
require 'commons-lang-2.3.jar'
require 'commons-codec-1.3.jar'
require 'xercesImpl-2.6.2.jar'
require 'xmlParserAPIs-2.6.2.jar'
require 'jaxen-1.1.1.jar'
require 'commons-collections-3.2.jar'
require 'js-1.6R7'
require 'nekohtml-0.9.5.jar'

# Include the Web Client class
include_class 'com.gargoylesoftware.htmlunit.WebClient';</pre>
            </td>
          </tr>
        </table>
        
        In this block, we first use the are telling JRuby to use the JAR files required by HtmlUnit.&nbsp; Some notes:
        
          * You have to specify every JAR file that HtmlUnit depends upon, even if you are not calling the method directly 
              * All JAR files must be in the LOAD\_PATH for JRuby.&nbsp; This is done by -I<DIR\_NAME> arguments passed in to JRuby from the command line. 
                  * The _include_class_ ****is similar to an import statement in Java and puts the WebClient object in scope.</ul> 
                At this point, we can now instantiate and use the WebClient class
                
                # Step 2: Parsing a Basic HTML Page
                
                Before we get into parsing a dynamic page, let&#8217;s take a look at how to parse a simple page.&nbsp; In this example, I am going to parse out information from the Maven 2 Archive for HtmlUnit found at [http://repo1.maven.org/maven2/htmlunit/htmlunit](http://repo1.maven.org/maven2/htmlunit/htmlunit "http://repo1.maven.org/maven2/htmlunit/htmlunit").
                
                <table cellspacing="0" cellpadding="0" width="400" border="0" unselectable="on">
                  <tr>
                    <td valign="top" width="400">
                      <pre class="code"># Function for getting a list of all directories
def get_htmlunit_maven_pages
  wc = WebClient.new;

  page = wc.getPage("http://repo1.maven.org/maven2/htmlunit/htmlunit");

  # List the directories...
  page.getByXPath('//img[@alt="[DIR]"]').each do |img|
    a = img.getNextSibling.getNextSibling
    puts 'DIR: ' + a.getHrefAttribute
  end

  # List the files...
  page.getByXPath('//img[@alt="[TXT]"]').each do |img|
    a = img.getNextSibling.getNextSibling
    puts 'FILE: ' + a.getHrefAttribute
  end
end</pre>
                    </td>
                  </tr>
                </table>
                
                The first step in the method is instantiating a new instance by calling _WebClient.new_ and then download the page using _wc.getPage._
                
                When requesting a page with a content type of text/html, the getPage call will return an instance of [HtmlPage](http://htmlunit.sourceforge.net/apidocs/com/gargoylesoftware/htmlunit/html/HtmlPage.html), and we can now use XPath expressions and DOM calls to get the URLs for the directories and for files.&nbsp; Very simple to get at the appropriate data.&nbsp; HtmlUnit has a bunch of other methods that you can use to navigate the page, check out the [source documentation for the HtmlPage](http://htmlunit.sourceforge.net/apidocs/com/gargoylesoftware/htmlunit/html/HtmlPage.html) object.
                
                # Step 3: Parsing Data Written By JavaScript Functions
                
                The code behind parsing a HTML page that uses JavaScript to dynamically create content is actually no harder than the previous example.&nbsp; HtmlUnit will detect the script tags in the page you are downloading and execute the appropriate script in line.&nbsp; For an example, I will use my [blog home page](http://www.innovationontherun.com) and its inclusion of a JavaScript widget from [MyBlogLog](http://www.mybloglog.com).&nbsp; This script makes a call to MyBlogLog and finds out who the most recent registered users to visit my site have been.&nbsp; In our example, we will parse out these users name and URLs.
                
                <table cellspacing="0" cellpadding="0" width="400" border="0" unselectable="on">
                  <tr>
                    <td valign="top" width="400">
                      <pre class="code"># Function for seeing who the most recent my blog log users were

def search_iotr
  wc = WebClient.new;

  page = wc.getPage("http://www.innovationontherun.com");
  my_blog_log_info = page.getHtmlElementById("MBL_COMM")
  my_blog_log_info.getByXPath('//td[@class="mbl_mem"]').each do |td|
    td.getByXPath('//a').each do |a|
      puts a.asText + ":" + a.getHrefAttribute
    end
  end
end
</pre>
                    </td>
                  </tr>
                </table>
                
                If you look at the source for this page, you will see a script tag that downloads a JavaScript file from MyBlogLog.com.&nbsp; The downloaded JavaScript will make calls to _document.write_ that will insert an HTML table into the page.&nbsp; The id of the table is MBL_COMM, so our first step is to find that HTML element.&nbsp; Once we have the element, it is a couple of simple XPath expressions to find the anchor tag that contains the recent visitors name and URL.&nbsp; All of the implementation of downloading the data and putting into the HTML page is hidden from us by HtmlUnit so we can easily use DOM to get at the information we are interested in.
                
                # Other Situations Where HtmlUnit Rocks!
                
                Anytime JavaScript is being used to either enable navigation or modify the HTML document, HtmlUnit can be a great asset in your parsing.&nbsp; This includes:
                
                  * Content from AJAX requests
                  * Situations where JavaScript events are being used to impact behavior.&nbsp; An example would be a page using an onChange handler on a select list to modify form values and/or submit the form.&nbsp; HtmlUnit is very handy for simplifying this interaction.
                
                A word of caution, the JavaScript implementation is not fully featured in HtmlUnit, so some sites still may not work.&nbsp; However, the HtmlUnit team is validating the browser against a fair number of popular libraries, so hopefully in future HtmlUnit releases, this will be less of an issue.
                
                # Appendix
                
                ## Prerequisite Information To Run the Example
                
                Make sure that you have Java installed.&nbsp; I am using Java 1.6, but HtmlUnit and JRuby should support older versions.
                
                Download JRuby from [http://dist.codehaus.org/jruby/](http://dist.codehaus.org/jruby/ "http://dist.codehaus.org/jruby/jruby-bin-1.0.1.tar.gz")&nbsp;and put the jruby executable (found in the bin directory of the downloaded file) in your path.
                
                To verify that Java and jruby are set up correctly, just run jruby from the command line and ask for the version:
                
                <pre class="code">&gt; jruby -version 
ruby 1.8.5 (2007-08-23 rev 4201) [x86-jruby1.0.1]</pre>
                
                ## My Environment Details
                
                  * JRuby version 1.0.1 
                      * HtmlUnit 1.13 
                          * Java version 1.6.0_02-b05 
                              * Ubuntu 7.0.4</ul> 
                            # Reference
                            
                              * [JRuby Home Page](http://jruby.codehaus.org/) 
                                  * [HtmlUnit Home Page](http://htmlunit.sourceforge.net) 
                                      * [HtmlUnit Java Doc](http://htmlunit.sourceforge.net/apidocs/index.html) 
                                          * [http://www.innovationontherun.com/jruby-scraper/scraper.tgz](http://www.innovationontherun.com/jruby-scraper/scraper.tgz "http://www.innovationontherun.com/jruby-scraper/scraper.tgz")&nbsp;&#8211; The source code for this example</ul>