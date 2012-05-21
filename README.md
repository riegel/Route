Route For HTML/OS
========================
Why?
------------------------
_Consider these two URL's_
<ol type="a"><li>http://example.com/blog</li>
<li>http://example.com/cgi-bin/htmlos.cgi/001149.3.317303135718820096/blog</li></ol>

Introducing Route
-----------------
This examples illustrates that it is desirable to have URL's that are easy to remember and easy to bookmark, oh and also search engine friendly.

Route provides HTML/OS powered websites a framework to keep URL's simple. This is oftentimes reffered to as a Model View Contoller or MVC.

This article will cover the following topics

* <a href="#top">Introduction to Route</a>
* <a href="#installing">Installing Route</a>
* <a href="#understanding">Understanding routes</a>
* <a href="#programming">Programming routes</a>
* <a href="#considerations">Additional Considerations</a>
* <a href="#underthehood">Under the hood</a>

_Note:_ Throughout this article we may use a capital R and other times a lowercase r when using the word route. Route with a capital R means the framework Route. And route with a lowercase r means a particular URL (or route) that the framework Route handles. So just remember Route handles routes.





Installation
=========================
Route is available as an Aestiva .bb file and can be downloaded here...

<a href="http://clearimageonline.com/home/route.bb">http://clearimageonline.com/home/route.bb</a>


System Requirements
-------------------------
Route utilizes the .htaccess file (for Apache) or web.config file (for IIS).

To use Route your server must read .htaccess (Apache) or web.config (IIS) files. The Route setup program will attempt to determine if .htaccess files are being parsed by your server. If they are not you will not be able to use Route.

_Note:_ Route for IIS (Windows) requires the URL Rewrite Module. <a href="http://www.iis.net/download/URLRewrite" target="_new">Information Found here</a>.




Installing route.bb
-------------------------
To install route use the Aestiva application installer.

1. From the Aestiva Desktop go to the Control Panel.
2. Click the Install Product Icon.
3. Type the path to the route.bb file in number 3, Transfer from Web Location <code>http://clearimageonline.com/home/route.bb</code>

Route setup
----------------------------------
Once it is installed you can run the Route setup by clicking the Route icon on the Main Menu.

The Route setup will modify the .htaccess (Apache) and (web.config) file found at the public root level of your HTML/OS installation. The setup program will make a backup automatically before overwriting it. Look for the backup in /apps/route/setup/back. The backup file will have a date/time stamp in its name.

_Note:_ If you have trouble with your Route installation, you should be able to remove the .htaccess and/or  web.config file from the root of your public folder. For detailed information about exactly what Route setup does, inspect /apps/route/setup/routesetup.log




Understanding routes
=========================
Flowchart
-------------------------
[/image/route?width=640&height=2000]

3 kinds of URL's
-------------------------
There are 3 kinds of URL's that we can have when using Route.

1. File path URL's<code>&lt;img src="/apps/myapp/images/logo.jpg"&gt;</code>These URL's are never handled by HTML/OS or Route they are handled by the web server (i.e. Apache, IIS)
2. cgi-bin Application URL's such as...<ul><li>Start links. Start links create a new session.<code>http://example.com/cgi-bin/start.cgi/index.html</code></li><li>getlink() and HTML/OS generated<code>http://example.com/cgi-bin/htmlos.cgi/001063.3.2061320743573834126</code></li></ul>
3. And new with <i>Route</i> we have <i>routes</i> like...<code>http://example.com/blog/view/25</code>

_Note:_ Having Route installed will not affect (unless the site relies on .htaccess or web.config files) an existing HTML/OS site. Existing HTML/OS sites will use cgi-bin style URL's and Route never sees these. This means you can install it with the confidence that everything will continue to work as normal. Then you can slowly add routes.




Programming routes
===========================
Handling routes
---------------
<h3>/apps/route/index.html</h3>

With Route installed you can edit /apps/route/index.html to program how your route URL's will be handled.

This program decides where each route should go. Use a goto to decide what program will handle any particular route. The following example uses a nested if then statement but you can use any control structures. For example a case statement would be suited very well for this.

<code>if tag_[1]='blog' then
 if tag\_[2]='' then
  goto '/apps/blog/index.html'
 else
  if tag\_[2]='list'
   blogcategory=tag\_[3] # <== Notice we use tag\_[3] to set a variable called blogcategory /#
   goto '/apps/blog/list.html'
  elif tag\_[2]='view' then
   blogpage=tag\_[3]
   goto '/apps/blog/view.html'
  elif tag\_[2]='play' then
   blogpage=tag\_[3]
   goto '/apps/blog/play.html'
  else
   goto error(404)
  /if
 /if
</code>

The following variables can be used to determine where each route will go.
<pre>
http://example.com/blog/view/521?var=y&var2=listmode

sysservicetype  http
domainname      example.com
tag             /blog/view/521?var=y&var2=listmode
tag_path        /blog/view/521
tag_vars        var=y&var2=listmode
tag_[1]         blog
tag_[2]         view
tag_[3]         521
</pre>



Coding routes into your pages
-----------------------------

Coding a route is similar to the way getlink works. Add something like this to your HTML files.

<code>&lt;a href="&lt;&lt;route('/blog/view/day1')&gt;&gt;"&gt;My first Day of School&lt;/a&gt;
or
&lt;&lt;
 display &lt;a href="^+route('/blog/view/day1')+^"&gt;My first Day of School&lt;/a&gt; /display
&gt;&gt;
or
&lt;a exithref="/blog/view/day1"&gt;My first Day of School&lt;/a&gt;</code>

The exithref approach does not consider if the end user isn't using cookies. the function route() takes this into account and will return a route that works even without cookies.

Coding routes into an overlay
-----------------------------
Instead of _goto page_ add something like this.
<code>goto route('/blog/view/'+blogpage)</code>





Additional Considerations
==========================
Search Engines
--------------------------
Search engines should be able to crawl a route site very quickly. Because Search engines do not accept cookies each visit to your site by a robot / search engine will generate a new session.

robots.txt
----------
Here is a suggested robots.txt file. It will tell search engines not to crawl any URL that starts with /cgi-bin. This has the benefit that all your route URL's will be crawled but not the normal HTML/OS links.
<code>User-agent: *
Disallow: /cgi-bin
</code>

409 Error
---------
Route will return a 409 ERROR if both a file path and a route are attempted. Generally this means you have added a folder or file at the root of your public folder that was not there when you did your initial Route setup. Reinstalling Route will recreate your web.config and .htaccess file and the error will go away, but it may make more sense to remove the conflicting file or folder. Optionally you may want to manually modify your .htaccess file.

Session Restarts
----------------
Route handles expired sessions automatically. There may be times when you need to force a session to restart. To do this you can manually type the following URL to create a new session.

<code>http://example.com/restart</code>

Using controllers
-----------------
Many use /apps/route/index.html for all their routes and are happy to keep them all there. However, at some point managing all of the routes necessary for a web site may become difficult to manage with a single file. This is where controllers may help save time.

Suppose you have an app defined at http://example.com/_blog_/.../... You can forward all requests where tag_[1]='blog' to another page that would then decide how to handle all the other tag\_'s

blog would be called a controller and you would go to it at the end of /apps/route/index.html like this...
<code>goto controller(tag_[1])</code>

Then create a file at /apps/route/controllers/_blog_.overlay

In this file you can add specific code for the app /blog

Route comes with 3 controllers pre-installed home.overlay, http_error.overlay, and restart.overlay

Here is what they contain...

<h4>home.overlay</h4>
<code>&lt;&lt;overlay restart
 # You can break your controllers out to .overlay files like this one /#
 goto '/apps/route/setup/working.html'
&gt;&gt;</code>

<h4>http_error.overlay</h4>
<code>&lt;&lt;overlay error
 goto error(tag_[2])
&gt;&gt;</code>


<h4>restart.overlay</h4>
<code>&lt;&lt;overlay restart
 temp=split(getlink('/apps/route/index.html'),'/')
 temp=temp[1,rows(temp)]
 ci\_cookiewrite('route\_controller',temp,adddays(now,-365))
 goto '/apps/route/restart.html'
&gt;&gt;</code>





Route Under The Hood
==================

Apache .htaccess File
---------------------
To use Route your server must read .htaccess files. The Route setup program will attempt to determine if .htaccess files are being parsed.

This is a sample of what the .htaccess file might look like. Not intended for actual usage. The Route installer will build a site specific .htaccess file for the HTML/OS installation.
<pre>  # Route Comments
  # .htaccess is a part of the Clear Image Media Routing Package for HTML/OS
  # contact riegel@clearimageonline.com with any questions
  #
  # copyright (c) July 2011
  # Update March 2012
  #
  # This .htaccess is autogenerated by /apps/route/setup/setup.html
  # use it to make modifications
  # /Route Comments

  RewriteEngine on

  #########################
  # Custom Errors
  #
  ErrorDocument 404 /apps/route/errors/404_error.html
  ErrorDocument 403 /apps/route/errors/403_error.html

  #########################
  # Test Rewrite Rules
  #
  RewriteRule ^apps/route/setup/test.txt$ /apps/route/setup/testROUTE.txt [L]

  #########################
  # Hidden CGI-BIN
  #
  RewriteRule ^([0-9]+\.[0-9]+\.[0-9]+)(.*)$ cgi-bin/htmlos/$1$2 [L]

  #########################
  # Cookie found rewrite rule
  #
  RewriteCond %{REQUEST_URI} !^/cgi-bin [NC]
  RewriteCond %{REQUEST_URI} !^/trash [NC]
  RewriteCond %{REQUEST_URI} !^/apps [NC]
  RewriteCond %{REQUEST_URI} !^/upload [NC]
  RewriteCond %{REQUEST_URI} !^/system [NC]
  RewriteCond %{HTTP_COOKIE} route_controller=([^;]+) [NC]
  RewriteRule ^(.*)$ /cgi-bin/htmlos/%1/_/$1 [L]

  #########################
  # Cookie NOT found rewrite rule
  #
  RewriteCond %{REQUEST_URI} !^/cgi-bin [NC]
  RewriteCond %{REQUEST_URI} !^/trash [NC]
  RewriteCond %{REQUEST_URI} !^/apps [NC]
  RewriteCond %{REQUEST_URI} !^/upload [NC]
  RewriteCond %{REQUEST_URI} !^/system [NC]
  RewriteCond %{HTTP_COOKIE} !^.*route_controller.*$ [NC]
  RewriteRule ^(.*)$ cgi-bin/htmlos/apps/route/startlink.html?_/$1 [L,QSA]

  # /Route Directives
</pre>



IIS web.config File
---------------------
Route for IIS requires the URL Rewrite Module. <a href="http://www.iis.net/download/URLRewrite">Information Found here</a>. To use Route your server must read and parse web.config files. The Route setup program will attempt to determine if web.config files are being parsed.

This is a sample of what the web.config file might look like. Not intended for actual usage. The Route installer will build a site specific web.config for the HTML/OS installation.
<pre>
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;!--
 Route Comments
 web.config is a part of the Clear Image Media Routing Package for HTML/OS
 contact riegel@clearimageonline.com with any questions

 copyright (c) July 2011
 Update March 2012

 This .htaccess is autogenerated by /apps/route/setup/setup.html
 use it to make modifications
 /Route Comments
--&gt;
&lt;configuration&gt;
 &lt;system.webServer&gt;
  &lt;httpErrors&gt;
   &lt;remove statusCode="403" subStatusCode="-1" /&gt;      
   &lt;remove statusCode="404" subStatusCode="-1" /&gt;                
   &lt;error statusCode="403" path="/apps/route/errors/403_error.html" responseMode="ExecuteURL" /&gt;
   &lt;error statusCode="404" path="/apps/route/errors/404_error.html" responseMode="ExecuteURL" /&gt;                
  &lt;/httpErrors&gt;
  &lt;rewrite&gt;
   &lt;rules&gt;
    &lt;rule name="Test Rewrite Rules" stopProcessing="true"&gt;
     &lt;match url="^apps/route/setup/test.txt$" ignoreCase="false" /&gt;
     &lt;action type="Rewrite" url="/apps/route/setup/testROUTE.txt" /&gt;
    &lt;/rule&gt;
    &lt;rule name="Hidden CGI-BIN" stopProcessing="true"&gt;
     &lt;match url="^([0-9]+\.[0-9]+\.[0-9]+)(.*)$" ignoreCase="false" /&gt;
     &lt;action type="Rewrite" url="cgi-bin/htmlos.exe/{R:1}{R:2}" /&gt;
    &lt;/rule&gt;
    &lt;rule name="Cookie found rewrite rule"&gt;
     &lt;match url="^(.*)$" ignoreCase="false" /&gt;
     &lt;conditions logicalGrouping="MatchAll"&gt;
      &lt;add input="{URL}" pattern="^/cgi-bin" negate="true" /&gt;
      &lt;add input="{URL}" pattern="^/trash" negate="true" /&gt;
      &lt;add input="{URL}" pattern="^/apps" negate="true" /&gt;
      &lt;add input="{URL}" pattern="^/upload" negate="true" /&gt;
      &lt;add input="{URL}" pattern="^/system" negate="true" /&gt;
      &lt;add input="{HTTP_COOKIE}" pattern="route_controller=([^;]+)" /&gt;
     &lt;/conditions&gt;
     &lt;action type="Rewrite" url="/cgi-bin/htmlos.exe/{C:1}/_/{R:1}" /&gt;
    &lt;/rule&gt;
    &lt;rule name="Cookie NOT found rewrite rule"&gt;
     &lt;match url="^(.*)$" ignoreCase="false" /&gt;
     &lt;conditions logicalGrouping="MatchAll"&gt;
      &lt;add input="{URL}" pattern="^/cgi-bin" negate="true" /&gt;
      &lt;add input="{URL}" pattern="^/trash" negate="true" /&gt;
      &lt;add input="{URL}" pattern="^/apps" negate="true" /&gt;
      &lt;add input="{URL}" pattern="^/upload" negate="true" /&gt;
      &lt;add input="{URL}" pattern="^/system" negate="true" /&gt;
      &lt;add input="{HTTP_COOKIE}" pattern="^.*route_controller.*$" negate="true" /&gt;
     &lt;/conditions&gt;
     &lt;action type="Rewrite" url="/cgi-bin/htmlos.exe/apps/route/startlink.html?_/{R:1}" appendQueryString="true" /&gt;
    &lt;/rule&gt;
   &lt;/rules&gt;
  &lt;/rewrite&gt;
 &lt;/system.webServer&gt;
&lt;/configuration&gt;
</pre>