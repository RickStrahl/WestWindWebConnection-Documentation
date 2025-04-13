The OnHttpBufferUpdate() method is called whenever a buffer is downloaded from the server and allows you to display download status information for large file downloads. It's also useful for freeing up the UI to avoid blocking HTTP calls that freeze the FoxPro UI when requests take too long.

You can either subclass the `wwHttp` class and override the `OnHttpBufferUpdate()` method, or you can use `BINDEVENT` to bind the method:


```foxpro
loHttp = CREATEOBJECT("wwHttp")
BINDEVENT(loHttp,"OnHttpBufferUpdate",THISFORM,"OnMyHttpBufferUpdateHandler")
```

The implementation of the `OnMyHttpBufferUpdateHandler()` then uses the signature described in the example below.