The wwScripting Response class provides Response class functionality to the script code embedded into the wwScripting class. This class allows the script user to access Response functions similar to the core Web Connection script features, so Response.Write, Response.Clear etc. are available. This class comes in two flavors:
<ul>
* **wwScriptingResponse**  
Provides base response behavior but without support for HTTP headers. This is the default.

* **wwScriptingHttpResponse**  
Provides the base functionality and adds the HTTP header behavior.
</ul>