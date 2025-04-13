This method allows you to add a custom route condition into your process class. It makes it possible to apply custom rules for method or page routing that overrides the default behavior that maps to a Process method or Script page.

This can be useful if you need to:

* Route multiple extensions into a single process class
* Re-use authentication for static files
* Do some unique URL handling

`OnRouting()` should be overridden, and return:

* .T. if the route was handled and a Response created
* .F. if the existing routing should be processed.

### Example
A common example is static help content that needs to be served alongside a Web Connection application, but also requires authentication. Because the Web Connection handler already implements authentication and you don't necessarily want to duplicate the auth configuration, you can run the static files through the existing Web Connection process and so gain authentication for the static content.

To do this you would:

* Add a new Web server script map extension
* Add the `OnRouting()` method
* Check for the extension (ie. `.html`)
* If found, route to a custom process method & return `.T.`
* If not found return .F.


#### Add IIS or Web Connection Web Server Scriptmap
So in the example above we can route `.html` documents into Web Connection with a script mapping in `web.config` or `WebConnectionWebServerSettings.xml`:

**In web.config**

```xml
<handlers>
      <add name=".html_wconnect-module" path="*.html" 
           verb="*" 
           type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" />
</handlers>           
```

**In WebConnectionWebServerSettings.xml**

```xml
<WebConnectionWebServerSettings>
   <HandledExtensions>.wc,.wcs,.wwd,.blog,.md,.html</HandledExtensions>
</WebConnectionWebServerSettings>
```

Then to handle the processing in your process class you add a method called `OnRouting()`:

```foxpro
FUNCTION OnRouting(lcPhysical, lcScriptname, lcExtension)
    IF (lcExtension == "html" OR lcExtension == "htm")
       THIS.HandleStaticHtmlFiles()
       RETURN .T.
    ENDIF
    
    RETURN .F.  && Continue processing stock routes
ENDFUNC

FUNCTION HandleStaticHtmlFiles()
    *** Just display the file from disk
    *** But if the class has authentication then auth is applied
    Response.Write(File2Var(Request.GetPhysicalPath())
ENDFUNC
```