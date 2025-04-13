The Web Application Virtual Path that marks the base path of this application. This path is used internally by ResolveUrl() to define the base path for a URL and the resolving of the ~ character in a path.

The value is a virtual path. For example:
<ul>
* /wconnect/
* /projects/bugreport/
</ul>

This value can be explictly set in your Process Initialization code, although this is not recommended.

By default this value is retrieved from the Server.oConfig.o<yourProcess>.cVirtualPath, which originates on the Process specific Configuration object defined in your <yourapplication>.Ini file.

* ** Process Config Section. 

The path is a LOGICAL path and should contain a trailing /.


If available this value is read from the process specific Config object. In wwProcess the following code exists:

```foxpro
IF TYPE("Server.oConfig.o" + THIS.Class +".cVirtualPath") = "C"
   THIS.cApplicationPath = EVALUATE( "Server.oConfig.o" + THIS.Class +".cVirtualPath")
ENDIF
```