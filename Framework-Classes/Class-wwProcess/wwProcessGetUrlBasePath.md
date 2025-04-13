Retrieves the virtual path of the current request. The value is provided with leading and trailing forward slashes. This path is used to resolve paths for [ResolveUrl()](vfps://Topic/_1O105V5UG).

**Result Example:** 
/wconnect/

Web Connection has to deduce this value and it is retrieved from one of these locations:

<ol>
* **APPL_MD_PATH ServerVariable**  
If this server variable exists it contains the ADSI path for the IIS virtual which includes the virtual name as the last path parameter (ie. /LM/W3SVC/1/ROOT**/wconnect**) and this method strips off this value and returns it as /wconnect/. If no trailing path is found past /ROOT it's assumed that the path is the root which returns /

* **Server.oConfig.oProcessClass Configuration.cVirtualPath Setting**  
The above server variable exists only on IIS 5 and later so older IIS servers and Apache don't have it. In that case the above configuration setting is used. This property corresponds to the INI file setting in your YourApp.ini file and the [YourProcessClass] and the VirtualPath= key.
</ol>