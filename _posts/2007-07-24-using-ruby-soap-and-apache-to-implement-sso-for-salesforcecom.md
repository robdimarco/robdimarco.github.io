---
id: 24
title: Using Ruby, SOAP, and Apache to implement SSO for SalesForce.com
date: 2007-07-24T21:02:31+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/using-ruby-soap-and-apache-to-implement-sso-for-salesforcecom/
permalink: /using-ruby-soap-and-apache-to-implement-sso-for-salesforcecom/
tags:
  - ruby
---
Since I have been talking about [Java vs. Ruby](http://www.redmonk.com/cote/2007/07/25/javas-fear-of-commitment/), I figured I would give a recent example of where Ruby really solved my problems simply and easily. &nbsp;My company uses [SalesForce.com](http://www.salesforce.com) for their [CRM](http://en.wikipedia.org/wiki/Customer_relationship_management) solution.&nbsp; Our sales, finance,&nbsp;and fulfillment&nbsp;teams have logins that are maintained and managed through the website.&nbsp; This is a pain for both the users and our IT team&nbsp;as people now have to remember their SalesForce username and password as well as their internal user name and password.

To combat this problem, SalesForce has created a method that allows [authentication of users via a SOAP request](http://wiki.apexdevnet.com/index.php/How_to_Implement_Single_Sign-On_with_Salesforce.com).&nbsp; Very cool, solves the problem, so I signed up to implement it.&nbsp; SalesForce supplies a WSDL documenting the services that need to be supported and&nbsp;you supply the URL that implements the defined services.&nbsp; Should be simple to connect to our ActiveDirectory server using LDAP to perform the validation of users.

# The Initial Attempt: Using Java Web Services&#8230;

Our standard development environment is Java 5,&nbsp;Maven 2 for builds,&nbsp;Apache 2 for HTTP(S), and JBoss 4.0.5 as an application server.&nbsp; I thought it would be a simple exercise to use [JAX-WS](https://jax-ws.dev.java.net/)&nbsp;combined with [JAX-WS Maven Plugin](https://jax-ws-commons.dev.java.net/jaxws-maven-plugin/)&nbsp;for auto-generation of my stubs and the&nbsp;[JBoss WS](http://labs.jboss.com/jbossws/)&nbsp;to simply deploy.&nbsp; First problem was getting the Maven plugin working correctly.&nbsp; After some trial and error, I got my POM setup correctly and was deploying the EAR.&nbsp; Unfortunately, I realized that JBoss was assuming that Tomcat was running on port 8080, causing a problem in the WSDL that was being referenced.&nbsp; So I had to find the JBoss property to tweak to fix that.&nbsp; Next problem wound up being that the default org.jboss.ws.soap.SOAPMessageImpl did not implement the setProperty method causing an _UnsupportedOperationException_.&nbsp; Awesome.&nbsp; To try to fix that issue, I wound up trying the following (in different combinations):

  * Upgrading JBoss to 4.2.1. 
      * Upgrading JBoss WS to 2.0 with JBoss 4.2.1 
          * Trying Java 6 as JAX-WS is in the standard JDK with both JBoss 4.0.5 and JBoss 4.2.1</ul> 
        With all of these combinations, I kept running into class loading issues and little help on the web.&nbsp; After much frustration, I said to hell with this, how can I do it in ruby&#8230;.
        
        # SOAP4R to the rescue
        
        [SOAP4R](http://dev.ctor.org/soap4r)&nbsp;is the best know SOAP library for ruby and the gem is easily installed
        
        > > gem install soap4r &#8211;source <http://dev.ctor.org/download/>
        
        Now I could auto-generate my stubs from the WSDL and put in my business logic.
        
        ## Generating Stubs from the WSDL
        
        In general, the [documentation](http://www.ruby-doc.org/stdlib/libdoc/soap/rdoc/index.html) for soap4r is non-helpful, but by looking at the source code and googling around a bit, I was able to get the gist.&nbsp; From the supplied SalesForce WSDL, I was able to quickly generate my stubs:
        
        > require &#8216;rubygems&#8217;  
        > gem &#8216;soap4r&#8217;  
        > require &#8216;wsdl/soap/wsdl2ruby&#8217;  
        > DIR = File.dirname(&#8220;.&#8221;)  
        > gen = WSDL::SOAP::WSDL2Ruby.new  
        > gen.basedir=File.dirname(DIR)  
        > gen.location=File.join(DIR,&#8221;AuthenticationService.wsdl&#8221;)  
        > gen.logger.level=Logger::DEBUG  
        > gen.opt[&#8216;classdef&#8217;] = &#8220;SforceAuth&#8221;  
        > gen.opt[&#8216;client_skelton&#8217;] = nil  
        > gen.opt[&#8216;servant_skelton&#8217;] = nil  
        > gen.opt[&#8216;cgi_stub&#8217;] = nil  
        > gen.opt[&#8216;standalone\_server\_stub&#8217;] = nil  
        > gen.opt[&#8216;mapping_registry&#8217;] = nil  
        > gen.opt[&#8216;driver&#8217;] = nil  
        > gen.opt[&#8216;force&#8217;] = true  
        > gen.run
        
        &nbsp;Running this produces seven files:
        
        <table cellspacing="0" cellpadding="1" width="600" border="1" unselectable="on">
          <tr>
            <td valign="top" width="239">
              <strong>File Name</strong>
            </td>
            
            <td valign="top" width="356">
              <strong>Purpose</strong>
            </td>
          </tr>
          
          <tr>
            <td valign="top" width="239">
              SforceAuthServant.rb
            </td>
            
            <td valign="top" width="356">
              Stub class that is used for the implementation.&nbsp; The server side implementation code will need to go in here.
            </td>
          </tr>
          
          <tr>
            <td valign="top" width="239">
              SforceAuthenticationService.rb
            </td>
            
            <td valign="top" width="356">
              If you would like to run a standalone server, this will be the file that you run.
            </td>
          </tr>
          
          <tr>
            <td valign="top" width="239">
              SforceAuthenticationService.cgi
            </td>
            
            <td valign="top" width="356">
              If you would like to use a web server (e.g. Apache) and CGI, this will be the file that you use.
            </td>
          </tr>
          
          <tr>
            <td valign="top" width="239">
              SforceAuthenticationServiceClient.rb
            </td>
            
            <td valign="top" width="356">
              If you would like to have a client to test your code, this will be the file that you run.
            </td>
          </tr>
          
          <tr>
            <td valign="top" width="239">
              SforceAuth.rb
            </td>
            
            <td valign="top" width="356">
              Class definitions for the request object and the response object.
            </td>
          </tr>
          
          <tr>
            <td valign="top" width="239">
              SforceAuthMappingRegistry.rb
            </td>
            
            <td valign="top" width="356">
              Class used to map SOAP requests and responses to ruby objects.
            </td>
          </tr>
          
          <tr>
            <td valign="top" width="239">
              SforceAuthDriver.rb
            </td>
            
            <td valign="top" width="356">
              Driver class used by the client to call into the server.
            </td>
          </tr>
        </table>
        
        &nbsp;
        
        ## Customizing the Generated Ruby Files to Get A Working System
        
        Of the seven files created, I needed three of them (the client, the standalone server, and the CGI script) needed to be executable.&nbsp; To get them to run, I needed to set the executable flag:
        
        > > chmod +x SforceAuthenticationService.rb SforceAuthenticationService.cgi SforceAuthenticationServiceClient.rb
        
        In addition, because I am using Ruby Gems to manage my dependencies, I needed to add the following two lines to the top of each of these three files:
        
        > require &#8216;rubygems&#8217;  
        > gem &#8216;soap4r
        
        To test the implementation, I started up the standalone authentication server (which listens on&nbsp;port 10080&nbsp;by default) and called it from the client.
  
        
        
        > > SforceAuthenticationService.rb & 
        > 
        > > SforceAuthenticationServiceClient.rb <http://localhost:10080></blockquote> 
        > 
        > If all is working, you will get a NoMethodError exception being thrown.&nbsp; To remedy this problem edit the SforceAuthServant.rb file and on line 15, change the line from _raise NotImplementedError.new_ to _{:authenticated => true}.&nbsp;_ Restarting the standalone server and rerunning the client will result in success. 
        > 
        > Now, I just needed to implement the code to call the LDAP server.&nbsp; First step was obtaining and installing the Gem for the Ruby&nbsp;[net-ldap](http://raa.ruby-lang.org/project/net-ldap/) project.&nbsp; Validating the username and password was as simple as putting this code into authenticate method for the SforceAuthServant class:
  
        > 
        > 
        > > def authenticate(parameters)
        > > 
        > > ldap = Net::LDAP.new  
        > > ldap.host = &#8220;ldap.hmsonline.com&#8221;  
        > > ldap.auth parameters.username[0], parameters.password[0]  
        > > if ldap.bind_as(  
        > > &nbsp;&nbsp;&nbsp; :base => &#8220;dc=com&#8221;,  
        > > &nbsp;&nbsp;&nbsp; :filter => &#8220;(sAMAccountName=&#8221;+parameters.username[0]+&#8221;)&#8221;,  
        > > &nbsp;&nbsp;&nbsp; :password => &#8220;&#8221;  
        > > )  
        > > &nbsp; return {:authenticated=>true}  
        > > else  
        > > &nbsp; return {:authenticated=>false}  
        > > end
        > > 
        > > end&nbsp;
        > 
        > Again, restarting the standalone server and using the client (with some tweaks to send the right parameters over) validated that the service was working successfully.
        > 
        > ## Configuring Apache to use the CGI
        > 
        > Obviously, I want SalesForce to use HTTPS when sending usernames and passwords.&nbsp; We use   
        > Apache as our web server for SSL.&nbsp; To get the CGI set up, I made sure that the directory with the files was accessible from the Apache configuration and then added a .htaccess file
        > 
        > > Options +ExecCGI  
        > > AddHandler cgi-script .cgi
        > 
        > Restarted Apache and I was done.&nbsp; The whole exercise took about an hour, and that was with me figuring out what was going on.&nbsp; The soap4r team has done a great job making setting up and and deploying a SOAP service&nbsp;quick, powerful, and easy to manage.