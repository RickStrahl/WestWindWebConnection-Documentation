Generic output method can be used to serve embedded resources like related image, css, javascript or other files through the application

This routine can be called with a URL like this:

/wconnect/WebResource.wwd?Resource=WcPowerLogo

where .wwd is the extension of your scriptmap. You can also access the resource like this:

/wconnect/wc.dll?MyProcess~WebResource~&Resource=WcPowerLogo

Resources can be of any type and can be added to the server at any time via the [wwServer::AddResource()](vfps://Topic/_1VZ03XRQD). This functionality is very useful for including related files of your application as part of the EXE rather than including the files externally in the Web directory. To use:
<ul>
* You add Resources typically in Server::OnLoad() 
* When you need a resource you link to the WebResource.wc url to retrieve it with the Resource= key to identify the resource
</ul>

Note that you can use [wwProcess::GetWebResourceUrl](vfps://Topic/_1VZ07TG8H) to get a URL returned in the current application that can be used to retrieve a resource.