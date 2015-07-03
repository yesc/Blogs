---
ID: 130
post_title: >
  Introducing the Thymeleaf template
  engine
author: Arnaud Cogoluègnes
post_date: 2013-01-24 10:00:00
post_excerpt: |
  <p>There's a bunch of Java template engines, but one of them has been getting some momentum these days: <a href="http://www.thymeleaf.org/">Thymeleaf</a>. Nice and powerful syntax, flexibility, vibrant community, and good integration with popular web technologies, these are all good reasons to discover this alternative to JSP. This article lists the core features of Thymeleaf and shows how to write and process an HTML template.</p>
layout: post
permalink: http://blog.zenika-offres.com/?p=130
published: true
---
<p>There's a bunch of Java template engines, but one of them has been getting some momentum these days: <a href="http://www.thymeleaf.org/">Thymeleaf</a>. Nice and powerful syntax, flexibility, vibrant community, and good integration with popular web technologies, these are all good reasons to discover this alternative to JSP. This article lists the core features of Thymeleaf and shows how to write and process an HTML template.</p>
<!--more-->
<h2>Thymeleaf in a nutshell</h2> <p>Thymeleaf is a Java template engine. It's an open source project and is licensed under the Apache License 2.0. Here are the core features of Thymeleaf:</p> <ul> <li>Simple and natural templating</li> <li>Optimized for web environment but can work standalone</li> <li>Advanced evaluation language (<a href="http://commons.apache.org/ognl/">OGNL</a> or <a href="http://static.springsource.org/spring/docs/3.2.x/spring-framework-reference/html/expressions.html">Spring Expression Language</a>)</li> <li>Support for template logic (condition, iteration)</li> <li>Full support for internationalization</li> <li>Spring MVC and Spring Web Flow integration</li> <li>Support for the composite view pattern (native with fragments, <a href="http://tiles.apache.org/">Tiles</a> integration, usable with Sitemesh)</li> <li>Sophisticated template caching support</li> <li>Works with <a href="http://dandelion.github.io/datatables/">Dandelion-Datatables</a></li> </ul> <p>Yes, that's a lot of features! Keep reading to discover how to write and feed a Thymeleaf template with data...</p> <h2>Setup of the template engine</h2> <p>Thymeleaf infrastructure is quite simple: a <code>TemplateResolver</code> to load templates and a <code>TemplateEngine</code> to do the actual processing (merging templates with a given context). Here is the code for a standalone setup:</p> <pre class="java code java" style="font-family:inherit">ClassLoaderTemplateResolver resolver = <span style="color: #7F0055; font-weight: bold;">new</span> ClassLoaderTemplateResolver<span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>; resolver.<span style="color: #000000;">setTemplateMode</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;XHTML&quot;</span><span style="color: #000000;">&#41;</span>; resolver.<span style="color: #000000;">setSuffix</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;.html&quot;</span><span style="color: #000000;">&#41;</span>; TemplateEngine engine = <span style="color: #7F0055; font-weight: bold;">new</span> TemplateEngine<span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>; engine.<span style="color: #000000;">setTemplateResolver</span><span style="color: #000000;">&#40;</span>resolver<span style="color: #000000;">&#41;</span>;</pre> <p>We're going to load the templates from the root of the classpath. When we ask Thymeleaf to render a template called <code>home</code>, the resolver will add the <code>html</code> extension, because we set the <code>suffix</code> property. We'll be then able to think in terms of logical names and won't worry about the physical name of the file.</p> <h2>The template</h2> <p>Our template is a <code>home.html</code> file located at the root of the classpath (e.g. in the <code>src/main/resources</code> directory of the project if we follow Maven conventions). The first version of the template contains only the skeleton an HTML page:</p> <pre class="html code html" style="font-family:inherit"><span style="color: #00bbdd;">&lt;!DOCTYPE html SYSTEM &quot;http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-3.dtd&quot;&gt;</span> <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">html</span> xmlns<span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;http://www.w3.org/1999/xhtml&quot;</span></span> <span style="color: #009900;">      xmlns:th<span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;http://www.thymeleaf.org&quot;</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">head</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">title</span>&gt;</span>My first template with Thymeleaf<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">title</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">meta</span> <span style="color: #000066;">http-equiv</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;Content-Type&quot;</span> <span style="color: #000066;">content</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;text/html; charset=UTF-8&quot;</span> <span style="color: #66cc66;">/</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">head</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">body</span>&gt;</span> &nbsp; <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">body</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">html</span>&gt;</span></pre> <p>Note the use of a Thymeleaf-specific doctype.</p> <h2>Processing of the template</h2> <p>There's nothing dynamic in our template, but we can test our setup by adding the following code after the engine configuration:</p> <pre class="java code java" style="font-family:inherit"><span style="color: #000000;">StringWriter</span> writer = <span style="color: #7F0055; font-weight: bold;">new</span> <span style="color: #000000;">StringWriter</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>; <span style="color: #000000;">Context</span> context = <span style="color: #7F0055; font-weight: bold;">new</span> <span style="color: #000000;">Context</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>; engine.<span style="color: #000000;">process</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;home&quot;</span>, context, writer<span style="color: #000000;">&#41;</span>;</pre> <p>The <code>writer</code> variable should then contain the output of the processed template, which is basically the static content of the file. Things that matter:</p> <ul> <li>The processing needs a context. This object will typically contain variables we want to display in the view.</li> <li>The template to render is called <code>home</code>. The resolver will be in charge of mapping this logical name with the physical location of the file. Remember we're using a classpath-based resolver which adds an HTML extension to the template name.</li> </ul> <p>OK, everything seems to work! See how Thymeleaf is lightweight. The template engine will be most of the time used in a web environment, but we can also easily use it in a standalone environment, like a JUnit test.</p> <h2>Labels and internationalization (i18n)</h2> <p>Thymeleaf's default internationalization support is quite simple: drop a properties file beside your template and you're done. Let's create a <code>home.properties</code> in the same directory as our template:</p> <pre> hello.world=Hello World! </pre> <p>And a <code>home_fr.properties</code> file for the French version:</p> <pre> hello.world=Bonjour le monde ! </pre> <p>We modify our template to refer to this label:</p> <pre class="html code html" style="font-family:inherit"><span style="color: #00bbdd;">&lt;!DOCTYPE html SYSTEM &quot;http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-3.dtd&quot;&gt;</span> <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">html</span> xmlns<span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;http://www.w3.org/1999/xhtml&quot;</span></span> <span style="color: #009900;">      xmlns:th<span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;http://www.thymeleaf.org&quot;</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">head</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">title</span>&gt;</span>My first template with Thymeleaf<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color
: #000000; font-weight: bold;">title</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">meta</span> <span style="color: #000066;">http-equiv</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;Content-Type&quot;</span> <span style="color: #000066;">content</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;text/html; charset=UTF-8&quot;</span> <span style="color: #66cc66;">/</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">head</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">body</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">p</span> th:<span style="color: #000066;">text</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;#{hello.world}&quot;</span>&gt;</span>Hello<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">p</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">body</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">html</span>&gt;</span></pre> <p>That's it, we unveiled how we insert dynamic content in Thymeleaf's templates: by placing extra attributes in HTML elements:</p> <pre class="html code html" style="font-family:inherit"><span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">p</span> th:<span style="color: #000066;">text</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;#{hello.world}&quot;</span>&gt;</span>Hello<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">p</span>&gt;</span></pre> <p>The nested <code>Hello</code> is there just for a preview, Thymeleaf will replace it by the dynamic value during processing. Note the use of a Thymeleaf's specific <code>th:text</code> attribute and the use of the <code>#{key}</code> syntax to refer to an entry of the property file.</p> <p>If we execute the rendering program, we end up with the following output (if your locale is something else than French!):</p> <pre class="html code html" style="font-family:inherit"><span style="color: #00bbdd;">&lt;!DOCTYPE html PUBLIC &quot;-//W3C//DTD XHTML 1.0 Strict//EN&quot; &quot;http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd&quot;&gt;</span> &nbsp; <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">html</span> xmlns<span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;http://www.w3.org/1999/xhtml&quot;</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">head</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">title</span>&gt;</span>My first template with Thymeleaf<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">title</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">meta</span> <span style="color: #000066;">http-equiv</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;Content-Type&quot;</span> <span style="color: #000066;">content</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;text/html; charset=UTF-8&quot;</span> <span style="color: #66cc66;">/</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">head</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">body</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">p</span>&gt;</span>Hello World!<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">p</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">body</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">html</span>&gt;</span></pre> <p>What about the French version? We can specify the locale in the context:</p> <pre class="java code java" style="font-family:inherit"><span style="color: #000000;">StringWriter</span> writer = <span style="color: #7F0055; font-weight: bold;">new</span> <span style="color: #000000;">StringWriter</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>; <span style="color: #000000;">Context</span> context = <span style="color: #7F0055; font-weight: bold;">new</span> <span style="color: #000000;">Context</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">Locale</span>.<span style="color: #000000;">FRANCE</span><span style="color: #000000;">&#41;</span>; engine.<span style="color: #000000;">process</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;home&quot;</span>, context, writer<span style="color: #000000;">&#41;</span>;</pre> <p>And Thymeleaf uses the appropriate properties file:</p> <pre class="html code html" style="font-family:inherit"><span style="color: #00bbdd;">&lt;!DOCTYPE html PUBLIC &quot;-//W3C//DTD XHTML 1.0 Strict//EN&quot; &quot;http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd&quot;&gt;</span> &nbsp; <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">html</span> xmlns<span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;http://www.w3.org/1999/xhtml&quot;</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">head</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">title</span>&gt;</span>My first template with Thymeleaf<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">title</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">meta</span> <span style="color: #000066;">http-equiv</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;Content-Type&quot;</span> <span style="color: #000066;">content</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;text/html; charset=UTF-8&quot;</span> <span style="color: #66cc66;">/</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">head</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">body</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">p</span>&gt;</span>Bonjour le monde !<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">p</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">body</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">html</span>&gt;</span></pre> <p>So far, so good, let's move on to the core topic: pushing objects in the context to render them in the view.</p> <h2>Variable substitution</h2> <p>Let's imagine we want to display the current date in the view. We can feed the context with an already-formatted string:</p> <pre class="java code java" style="font-family:inherit"><span style="color: #000000;">String</span> now = <span style="color: #7F0055; font-weight: bold;">new</span> <span style="color: #000000;">SimpleDateFormat</span><span style="color
: #000000;">&#40;</span><span style="color: #888888;">&quot;yyyy-MM-dd&quot;</span><span style="color: #000000;">&#41;</span>.<span style="color: #000000;">format</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">Calendar</span>.<span style="color: #000000;">getInstance</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>.<span style="color: #000000;">getTime</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span><span style="color: #000000;">&#41;</span>; context.<span style="color: #000000;">setVariable</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;date&quot;</span>, now<span style="color: #000000;">&#41;</span>;</pre> <p>And then refer to this variable in the template like the following:</p> <pre class="html code html" style="font-family:inherit"><span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">p</span> th:<span style="color: #000066;">text</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;${date}&quot;</span>&gt;</span>The date<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">p</span>&gt;</span></pre> <p>Note the use of the <code>${variable}</code> syntax this time (we used <code>#{...}</code> for i18n). We're again relying on the <code>th:text</code> attribute to inject dynamic content. If we process the template again, the output contains the expected content:</p> <pre class="html code html" style="font-family:inherit"><span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">p</span>&gt;</span>2013-01-18<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">p</span>&gt;</span></pre> <p>Let's see how to display a typical domain object.</p> <h2>Variable substitution with a Java bean</h2> <p>Displaying domain objects is a common use case of web applications. We can add a <code>Contact</code> object for display to the Thymeleaf context:</p> <pre class="java code java" style="font-family:inherit">context.<span style="color: #000000;">setVariable</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;contact&quot;</span>,<span style="color: #7F0055; font-weight: bold;">new</span> Contact<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;John&quot;</span>,<span style="color: #888888;">&quot;Doe&quot;</span><span style="color: #000000;">&#41;</span><span style="color: #000000;">&#41;</span>;</pre> <p>We hard-coded the domain object state, but it could be as well loaded from a database. Here's how to display the <code>firstname</code> and <code>lastname</code> properties of the domain object:</p> <pre class="html code html" style="font-family:inherit"><span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">div</span>&gt;</span>      <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">p</span>&gt;</span>First name: <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">span</span> th:<span style="color: #000066;">text</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;${contact.firstname}&quot;</span>&gt;</span>First name<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">span</span>&gt;&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">p</span>&gt;</span>      <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">p</span>&gt;</span>Last name: <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">span</span> th:<span style="color: #000066;">text</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;${contact.lastname}&quot;</span>&gt;</span>Last name<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">span</span>&gt;&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">p</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">div</span>&gt;</span></pre> <p>As you can see, the content of <code>${...}</code> in Thymeleaf can be a complex expression, not only the reference to variable. Thymeleaf uses <a href="http://commons.apache.org/ognl/">OGNL</a> for its default processing engine, opening a wide range of possibilities: operators, concatenation, etc.</p> <p>Let's see now an extra feature that can make the display of Java object shorter.</p> <h2>Less verbosity with the selection syntax</h2> <p>Thymeleaf allows to perform a <em>selection</em> on objects. Once an object has been selected, it's available as some kind of first-level variable in the evaluation context. We can then refer to it using the <code>*{...}</code> syntax instead of the <code>${...}</code> syntax.</p> <p>In our template, we can select the <code>contact</code> object with the usual <code>${...}</code> syntax and then refer to its properties with the <code>*{...}</code> syntax:</p> <pre class="html code html" style="font-family:inherit"><span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">div</span> th:<span style="color: #000066;">object</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;${contact}&quot;</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">p</span>&gt;</span>First name: <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">span</span> th:<span style="color: #000066;">text</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;*{firstname}&quot;</span>&gt;</span>First name<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">span</span>&gt;&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">p</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">p</span>&gt;</span>Last name: <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">span</span> th:<span style="color: #000066;">text</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;*{lastname}&quot;</span>&gt;</span>Last name<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">span</span>&gt;&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">p</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">div</span>&gt;</span></pre> <p>We end up with the exact same rendering, but the template code is more simple. Nice, isn't it?</p> <h2>Iteration</h2> <p>Another common use case of web applications is the display of data in tables: we load a list of Java objects from the database and display them in a HTML table. Imagine we feed our context with a <code>List&lt;Contact&gt;</code>:</p> <pre class="java code java" style="font-family:inherit"><span style="color: #000000;">Context</span> context = <span style="color: #7F0055; font-weight: bold;">new</span> <span style="color: #000000;">Context</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>; List<span style="color: #000000;">&lt;</span>Contact<span style="color: #000000;">&gt;</span> contacts = <span style="color: #7F0055; font-weight: bold;">new</span> ArrayList<span style="color: #000000;">&lt;</span>Contact<span style="color: #000000;">&gt;</span><span style="color: #000000;">&#40;</span><span style="color: #000000;">&#41;</span>; contacts.<span style="color: #000000;">add</span><span style="color: #000000;">&#40;</span><span style="color: #7F0055; font-weight: bold;">
new</span> Contact<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;John&quot;</span>,<span style="color: #888888;">&quot;Doe&quot;</span><span style="color: #000000;">&#41;</span><span style="color: #000000;">&#41;</span>; contacts.<span style="color: #000000;">add</span><span style="color: #000000;">&#40;</span><span style="color: #7F0055; font-weight: bold;">new</span> Contact<span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;Jane&quot;</span>,<span style="color: #888888;">&quot;Doe&quot;</span><span style="color: #000000;">&#41;</span><span style="color: #000000;">&#41;</span>; context.<span style="color: #000000;">setVariable</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;contacts&quot;</span>,contacts<span style="color: #000000;">&#41;</span>; engine.<span style="color: #000000;">process</span><span style="color: #000000;">&#40;</span><span style="color: #888888;">&quot;home&quot;</span>, context, writer<span style="color: #000000;">&#41;</span>;</pre> <p>The iterate over the list, we use the <code>th:each</code> attribute:</p> <pre class="html code html" style="font-family:inherit"><span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">table</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">tr</span>&gt;</span>         <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">th</span>&gt;</span>Firstname<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">th</span>&gt;</span>         <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">th</span>&gt;</span>Lastname<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">th</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">tr</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">tr</span> th:each<span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;contact : ${contacts}&quot;</span>&gt;</span>          <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">td</span> th:<span style="color: #000066;">text</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;${contact.lastname}&quot;</span>&gt;</span>The first name<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">td</span>&gt;</span>          <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">td</span> th:<span style="color: #000066;">text</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;${contact.firstname}&quot;</span>&gt;</span>The last name<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">td</span>&gt;</span>      <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">tr</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">table</span>&gt;</span></pre> <p>We end up with this output:</p> <pre class="html code html" style="font-family:inherit"><span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">table</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">tr</span>&gt;</span>         <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">th</span> <span style="color: #000066;">rowspan</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;1&quot;</span> <span style="color: #000066;">colspan</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;1&quot;</span>&gt;</span>Firstname<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">th</span>&gt;</span>         <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">th</span> <span style="color: #000066;">rowspan</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;1&quot;</span> <span style="color: #000066;">colspan</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;1&quot;</span>&gt;</span>Lastname<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">th</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">tr</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">tr</span>&gt;</span>          <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">td</span> <span style="color: #000066;">rowspan</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;1&quot;</span> <span style="color: #000066;">colspan</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;1&quot;</span>&gt;</span>Doe<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">td</span>&gt;</span>          <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">td</span> <span style="color: #000066;">rowspan</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;1&quot;</span> <span style="color: #000066;">colspan</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;1&quot;</span>&gt;</span>John<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">td</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">tr</span>&gt;&lt;<span style="color: #000000; font-weight: bold;">tr</span>&gt;</span>          <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">td</span> <span style="color: #000066;">rowspan</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;1&quot;</span> <span style="color: #000066;">colspan</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;1&quot;</span>&gt;</span>Doe<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">td</span>&gt;</span>          <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">td</span> <span style="color: #000066;">rowspan</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;1&quot;</span> <span style="color: #000066;">colspan</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;1&quot;</span>&gt;</span>Jane<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">td</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">tr</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">table</span>&gt;</span></pre> <p>Note Thymeleaf added some <code>rowspan</code> and <code>colspan</code> attributes in accordance with the DTD for the selected XHTML 1.0 Strict standard. Thymeleaf wouldn't generate them if we have told it to be compliant to HTML 5.</p> <p>Before finishing our discovery of Thymeleaf, let's play with conditional statements.</p> <h2>Conditional, "if" syntax</h2> <p>Imagine we don't want to display an empty table if the list of contacts is empty, we can easily check the size of the list and choose to display the whole table o
nly if the list isn't empty:</p> <pre class="html code html" style="font-family:inherit"><span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">table</span> th:if<span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;${not #lists.isEmpty(contacts)}&quot;</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">tr</span>&gt;</span>         <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">th</span>&gt;</span>Firstname<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">th</span>&gt;</span>         <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">th</span>&gt;</span>Lastname<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">th</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">tr</span>&gt;</span>     <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">tr</span> th:each<span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;contact : ${contacts}&quot;</span>&gt;</span>          <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">td</span> th:<span style="color: #000066;">text</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;${contact.lastname}&quot;</span>&gt;</span>The first name<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">td</span>&gt;</span>          <span style="color: #009900;">&lt;<span style="color: #000000; font-weight: bold;">td</span> th:<span style="color: #000066;">text</span><span style="color: #66cc66;">=</span><span style="color: #ff0000;">&quot;${contact.firstname}&quot;</span>&gt;</span>The last name<span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">td</span>&gt;</span>      <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">tr</span>&gt;</span> <span style="color: #009900;">&lt;<span style="color: #66cc66;">/</span><span style="color: #000000; font-weight: bold;">table</span>&gt;</span></pre> <p>Note the use of the <code>#lists</code> utility objects to check whether the list is empty or not.</p> <h2>Conclusion</h2> <p>This article introduced the basic templating features of Thymeleaf. Thymeleaf is meant to be used in web applications, but its flexibility let us use it in a standalone environment. Thymeleaf doesn't need a JSP compiler and can fetch templates from anywhere (file system, web context, classpath). You can even package your whole web application in a simple JAR, that's a great step towards modularity! Thymeleaf has tons of features and we'll see how to integrate it with Spring MVC in a subsequent post.</p> <p><a href="https://github.com/acogoluegnes/blog-thymeleaf">Source code</a></p>