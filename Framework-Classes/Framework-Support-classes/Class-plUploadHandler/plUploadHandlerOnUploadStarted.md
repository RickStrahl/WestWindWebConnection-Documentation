This method is called when an individual file upload is started. 

You can override this method in a subclass or point at a function/method using the cOnUploadStartedFunction. 


**Note: **You can hook this event without subclassing by using FoxPro's BindEvent():

```foxpro
BINDEVENT(loUpldate,"OnUploadStarted",THIS,"OnUpdateStarted",1)
```