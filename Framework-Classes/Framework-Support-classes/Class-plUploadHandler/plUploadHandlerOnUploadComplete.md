This method is called when an upload of an individulal flie is complete. The filename is passed and you can then decide what to do with the file.

You can override this method to provide custom behavior in a subclass or implement the cOnUploadCompleteFunction() which fires a function with the same signature as this method.  One of these typically needs to be implemented in order to do something useful with an uploaded file.

The overridden method should return a result string that is marshalled back to the JavaScript client - very commonly a URL to the uploaded file. 

When overridden this method can return a string value that is passed back to the JavaScript client's completion method. Use WriteCompleteResponse() to write out that response.

**Note: **You can hook this event without subclassing by using FoxPro's BindEvent():

```foxpro
BINDEVENT(loUpldate,"OnUploadComplete",THIS,"OnUpdateComplete",1)
```