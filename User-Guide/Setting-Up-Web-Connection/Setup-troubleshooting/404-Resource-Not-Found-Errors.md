IIS often throws 404 (Resource Not Found) errors in response to configuration issues. 404 errors are primarily meant to indicate that an invalid URL was referenced pointing to a script or page that doesn't exist. However, IIS also uses this error message if some resource is not configured correctly with permissions.

Here are a few things that can trigger 404 errors

* **File not found**  
A 404 error is the correct and obvious error result for this. If you get a 404 the first thing you should check is to make sure that you typed the URL correctly and that the script or file you are accessing on the server indeed exists as a file, or - if you are using a Script map - that the method in the Process class you are referencing exists.

* **ISAPI Handler Not Registered**  
ISAPI extensions in IIS must be registered as allowed executables as does ASP.NET as a feature. You can find this feature in the IIS Management Console on the server page in **ISAPI and CGI Restrictions**:
![](/images/misc/ISAPIRestrictions.png)
If you're using ASP.NET make sure that your ASP.NET version (4.x most likely) is allowed. If you're using ISAPI make sure you explicit DLL is allowed (highlighted in the example).

* **IIS Request Filtering**  
IIS 7 and later introduces native request filtering rules which are defined in `ApplicationHost.config`. If any of the rules kick in to prevent a request from being fired a 404 error occurs. One rule specifically that affects Web Connection is direct access to /bin/wc.dll - in the BIN folder. Urls like `http://localhost/wconnect/bin/wc.dll` will fail due a restriction that doesn't allow direct access to any executables in the BIN folder. Rules are defined in `%SystemDir%\inetsrv\config\applicationhost.config` in the requestFiltering section.   

  The recommended work around for this problem is to [use script maps](VFPS://Topic/_0G013MUOO).

* **IIS Request Limits**  
If you exceed Request lmits IIS throws 400 errors. Urls that are to long or include relative paths that move below the virtual root folder throw 400 errors. Extensive query strings can also result in 404 errors.