The Response class used for script processing. 
<ul>
* **wwScriptingResponse**  
This is the default Response class used that provides the base functionality. It includes functionality for writing output and clearing output.

* **wwScriptingHttpResponse**  
Subclassed from wwScriptingResponse this class adds support for HTTP headers operations using AppendHeader, AddCookie, Status, ContentType and a RenderHttpHeader() method that can be called to retrieve the HTTP Header.
</ul>