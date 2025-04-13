To create an ASP component from a running  Web Connection Application you should create a new project with a different name so as to not disturb the existing Out Of Process COM component registration. 

Assume for a second that you have an existing Web Connection project WebDemo (see Step by Step Guide). To convert the project into an ASP project follow these steps:

<ul>
* Create a new project for you application. CREATE PROJECT ASPWebDemo and add WebDemoMain. 
* Rebuild the project - you may have to run your application once in order to set the path and environment to find all your required project files. Make sure Rebuild works without errors before continuing.
* Make sure you set the DEBUGMODE flag in wconnect.h to .F.
* Compile the project into an MTDLL or use BUILD MTDLL ASPWebDemo FROM ASPWebDemo (reqiures VFP SP3!)
</ul>

There are two ways that you can call your WC components from an ASP page:

<ul>
* **As a fully self-contained page just like a regular WC request**  
With this mechanism you simply have an ASP loader page that loads your Web Connection component and runs the request. The WC component uses the existing ASP Request and Response objects to handle its program inputs and outputs. The loader page will always be the same and looks something like this (wc.asp):

<pre><% 
  Response.Buffer = True
  Set oServer = Server.CreateObject("ASPWebDemo.WebDemoServer")

  oServer.ProcessHit()  ' writes its own output!

  ' Response.Write(oServer.cErrorMsg)  && for troubleshooting
%>
</pre>

A URL to this page looks very much like a standard Web Connection request except that you use wc.asp instead of wc.dll:

<pre>/wconnect/wc.asp?webprocess~HelloWorld</pre>

* **As an embeddable component inside of a larger ASP Page**  
You can also embed calls to a Web Connection component into an ASP that performs other processing or contains additional text. To so create your ASP page and add ASP script calls to the ASPCallRequest method of your server:

<pre><% 
  Response.Buffer = True
  Set oServer = Server.CreateObject("ASPWebDemo.WebDemoServer")
%>

This is some text in an ASP document with some ASP script <%= now %>...
<p>

Following is some text called from a WC request:<p>
<b><% oServer.ASPCallRequest "wwDemo","HelloASP"  %></b>
<p>
More plain Text from the ASP page.
<P>
Followed by another WC hit:<p>
<b><% oServer.ASPCallRequest  "wwdemo","EndASP" %></b>
</pre>
</ul>


<blockquote>**Note**  
Because ASP is already a script map, you cannot use the script map syntax feature of Web Connection to route requests. So requests like HelloWorld.wp don't work. Instead the full URL syntax must be used:

<pre>wc.asp?WebProcess~HelloWorld~positionalparm~&namedparm1=value1&NamedParm2=100</pre>

Note that you can still use either positional or named parameters - just make sure you delimit the named parameters and positional parameters with both a terminating ~ and starting &.</blockquote>

A few notes:
<ul>
* Both ProcessHit and ASPCallRequest create output directly into the ASP output stream so these methods do not return anything.
* Performance of ASP operation will be considerably slower than running using the Web Connection ISAPI DLL to manage requests.
* Debugging ASP components can be difficult. I suggest you get your app running with Web Connection's filebased messaging then test operation under ASP. Other than the wc.dll change to wc.asp most features should work identically between File/COM and ASP messaging.
* Scriptmaps don't work with ASP messaging because ASP is already a script map. You always need to use long hand syntax for parameters: wc.asp?wwdemo~TestPage~&NamedParameter=Rick
* Complex HTTP Header operations may not work correctly with ASP Messaging (see below).
</ul>


### A note about HTTP Headers
This works and it actually works rather well. You can push data to the server easily here and the majority of requests work perfectly well. It even works in most cases if a custom WC HTTP header is created as long as the Response::ContentTypeHeader method is called at some point (which includes implicit calls to methods like HTMLHeader, ExpandTemplate/Script,StandardPage etc.). 

The problem is with headers that don't follow this latter rule. Basically, Web Connection overrides ContentTypeHeader() for ASP and captures the outbound HTTP header and then writes the header out using the ASP object using Response.ContentType and Response.AddHeader. This requires that HTTPHeader() is always stored as a string and never passed an output wwResponse object that writes to file. This unfortunately is required for ASP, because ASP cannot write a full raw HTTP response or even raw headers as Web Connection does.