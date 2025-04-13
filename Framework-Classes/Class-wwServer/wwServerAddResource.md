Allows adding an embeddable resource like an image, CSS or JS file or other file to be stored and then served as a WebResource url of the application. 

To use this functionality you call `Server.AddResource()` to add images, .js, .css, .htm files etc. to the resource pool which is stored in Server.oResources. You can then retrieve these Resources via Server url by calling:

```txt
WebResources.wwd?Resource=Hello 
```

where `.wwd` is your application's script map. WebResources is available on any Process class implementation.

This functionality allows you to minimize external dependencies in your application and lets you 'embed' things like style sheets, images and JavaScript resources into your exe. The resources are then dynamically loaded through a Web Connection URL.

Note this approach causes extra hits on your Web Connection server and it's more expensive to serve requests this way than letting the Web Server manage the resources and caching. Returning reseources is efficient - they are cached internally and maximum caching is applied in the browser.