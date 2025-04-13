Extensionless URLs are common in today's Web applications. Extensionless URLs are URLs that don't point at a specific scriptmap or 'file', but rather treat URLs as resources that are typically marked by nouns. A typical extensionless URL might be:
 
* http://localhost/wconnect/customers
* http://localhost/wconnect/customers/3232 

The URLS don't point at 'page' but just at what appears to be a path. Routing should allow us to access the segments off that path past the base path and identify the `customers` and `3232` segements in the second URL.

### Implementing 'Routing' via Url Redirection in IIS 7
IIS 7 includes a <a href="http://learn.iis.net/page.aspx/460/using-the-url-rewrite-module/" target="top">UrlRewrite Module</a> (installable via the <a href="http://www.microsoft.com/web/downloads/platform.aspx" target="top">Web Platform Installer</a>) and you you can use this module to route extensionless URLs to a URL of your choice. 

### Implementing Routing in your Web Connection Process Class
You can rewrite to any URL of your choice, but this example assumes you are using the built-in `wwProcess::UrlRewriteHandler` method accessible with UrlRewriteHandler.wwd (use your scriptmap instead of `.wwd`) which provides some useful base functionality by creating a RoutingInfo structure that contains the path segments and the original user URL.

`UrlRewriteHandler` then forwards to [wwProcess::OnUrlRewrite](VFPS://Topic/_4GM0XIJR7) which you can override to handle the route. Typically, you'll then figure out the method to call based on one of the path segments and use a Fox `EVALUATE()` to call it.

### Setting up a UrlRewrite Rule
To make extensionless URLs work we can configure a UrlRewrite rule in IIS by looking at all URLs in a given path and then rewriting that URL to a known endpoint in your app. We include only extensionless URLs and ignore files and directories.

Your create a rewrite rule like this in web.config in the <system.webServer> section:

```xml
<configuration>
     <system.webServer>
        <rewrite>
          <rules>
            <rule name="ExtensionlessUrl" patternSyntax="ECMAScript" stopProcessing="true">          
              <!-- match all URLs and use physical file exclusion conditions below -->
              <!--<match url=".[^.]*$"  />-->
              
              <!--  exclude all URLs that contain a . -->
              <match url="^((?!\.).)*$" />
              
              <conditions>
                <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
                <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
              </conditions>
              <action type="Rewrite" url="UrlRewriteHandler.wwd" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>     
   </system.webServer>
</configuration>
```

This rewrite rule basically looks for any URL that does not have '.' in the URL, nor points at any physical file or directory on disk. It then then routes it to UrlRewriteHandler.wwd (use your scriptmap instead of `.wwd`).

The Regex expression matches any character except for a . in the URL. If it finds a period it ignores the URL.

> Note that there's a second, commented expression. The active one won't work if you have folders with a `.` in the path. If you do you can try the other expression which matches all URLs and filters out physical files and folders from a match.

### Implement OnUrlRewrite in your Process Class
Web Connection includes `UrlRewriteHandler()` and `OnUrlRewrite(loRewrite, lvParm)` methods. The former is the entry point method that receives the initial request and should be considered internal. It creates a Route object and forwards it to `OnUrlRewrite(loRewrite, lvParm1)`. The `loRewrite` object contains original full url, the original path only and a collection of each of the path segments relative to the virtual.directory.


<table class="detailtable" style="width:96%; margin-bottom: 10px;">
<tr><th>Property</th><th>Functionality</th></tr>
<tr>
  <td><b>lIsRouted</b></td>
  <td>Determines whether the current request was rewritten by IIS.</td>
</tr>
<tr>
  <td><b>cOriginalUrl</b></td>
  <td>The original extensionless URL that triggered the rewrite. Full server relative path that includes the query string. <br/>
<i>/wconnect/customers/3211?parm=val</i></td>
</tr>
<tr>
  <td><b>cOriginalPath</b></td>
  <td>The original extensionless URL that triggered the re-write, without query string.<br/>
<i>/wconnect/customers/3211</i></td>
</tr>
  <td><b>oPathSegments</b></td>
  <td>Each of the path's segments relative to the virtual directory or root in a wwCollection instance. Note that segments only include segments after the virtual path of the base site/virtual.<br/>
<i>Two segments: `customers` and `3211`<br/>
lcId = loRewrite.oPathSegments.Item(2)</i></td>
</tr>
</table>  
  
Keep in mind that the re-written URL essentially is the 'real' URL as far as your Web Connection request is concerned so the current path with Request.GetLogicalPath() will always be the redirect url (/wconnect/UrlRewriteHandler.wwd typically). Note that Request.QueryString() and Request.Form() objects work as expected with POSTed or query string data. loRewrite provides you information of the user's navigation and so you need to typically use the original url or its path segments to determine what action to take.

### Override OnUrlRewrite() to create custom routing
Typically when using extensionless URLs you'll need to parse the path and map each segment to an operation or parameter.

Here's a simple example that takes parameterless URLs and re-routes them to methods of the same name as the first segment. For example, /wconnect/testpage will be routed to /wconnect/testpage.wwd. 

In this example I'm going to handle two URLs: `/wconnect/customers` and `/wconnect/customers/West+Wind` - a list URL and a detail URL respectively.

The actual `oPathSegments` are:

* customers
* customers/West+Wind

Here's the code to handle these endpoints:

```foxpro
*** Create a property to save Rewrite object
*** so it can be accessed by the called methods
oRewrite = NULL

************************************************************************
* wwDemo ::  OnUrlRewrite
****************************************
FUNCTION OnUrlRewrite(loRewrite, lvParm1)
LOCAL llRouteError, lvResult

*** OPTIONAL - This makes the rewrite object available to process methods
***            so you can read path segments from the collection
this.oRewrite = loRewrite

*** Assume second segment is our method name
IF loRewrite.oPathSegments.Count > 0
   lcMethod = loRewrite.oPathSegments.Item(1)   
   
   llRouteError = .F.
   lvResult = .F.
   TRY   
	   lvResult = EVALUATE("THIS." + lcMethod  + "()")   
   CATCH TO loException
   	   llRouteError = .T.
	   this.ErrorMsg("Invalid Route",;
		   "Couldn't find a matching route for this URL:  " + loException.Message)
   ENDTRY
   
   IF llRouteError
       RETURN  && error message displayed
   ENDIF 
   RETURN lvResult  && usually .T. and unused except for auth
ENDIF

this.ErrorMsg("Invalid Route",;
   "Route values must at least include 1 segments relative to the virtual or root application")

ENDFUNC

***********************************************************************
*  wwDemo :: Customers
****************************************
FUNCTION Customers()


*** Retrieve second parameter segment and query
*** Returns null if there is no such segment
lcId = this.oRewrite.oPathSegments.Item(2)


*** No id passed - display a list
IF ISNULLOREMPTY(lcId)
	SELECT * FROM tt_cust ;
	   INTO CURSOR TQuery ;
	   ORDER BY Company   
	lcHtml = HtmlDataGrid("TQuery")
ELSE
	SELECT * FROM tt_cust ;
	   WHERE company = lcId ;
	   INTO CURSOR TQuery
    lcHtml = HtmlRecord("TQuery")
ENDIF

THIS.Standardpage("Show Customer",lcHtml)

* RETURN  && not required
ENDFUNC
*   Customers
```

Using this code you can now do:

* http://localhost/wconnect/customers
* http://localhost/wconnect/customers/West+Wind+Technologies

and get a customer list displayed for the first and a specific customer for the second.

Keep in mind this is a very basic implementation that uses the first path segment (Customer or TestPage) to map the action, and stores the loRewrite object in a property on the Process class. The target method that is called (Customer) then uses the persisted Rewrite object and the second path segment to determine which customer to load.

A typical solution will need two pieces: 
* The routing logic that somehow figures out which methods to call based on the URL segments
* And the implementation that potentially uses other route segments as parameters

Ultimately it's up to you how you implement this but there are lots of options on how you can work with the data passed to you from the UrlRewrite module.

### Parameter Handling for API Requests
If you are using extensionless URLS for API or SOAP service calls you need to implement the logic to handle the lvParm1 parameter. JSON service API calls often receive a JSON single parameter that is passed to the target method and lvParm1 has to be passed through. The code in the last example demonstrates how to check weather the value is set.

> #### @icon-warning Plain JSON True/False Parameters don't work
>Note that if the client sends a Logical value as a parameter will fails, since the check for logical is used as the test to determine whether the value exists. If you need to have logical requests on the server either wrap the value into an object, or use a numeric (0,1) rather then true or false as the parameter value.