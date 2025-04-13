At the low level you can use this class to make a server method callback with the following code:
```cs
var Manual = new AjaxMethodCallback("Callback");

// * ** specify where to call back to - note you can send to another page!
Manual.serverUrl = "SimpleMethodcallbacks.aspx";   // required in manual code

// * ** You can route a callback to a specific server control. By default 
// * ** calls are routed to the Page, but any user or custom control can
// * ** be referenced by it's server ID. Optional - 
// Manual.targetControlId = "MyControl"

// * ** You can specify how data is posted back. Get, Post, PostNoViewState and parameters only (default)
Manual.postbackMode = "PostMethodParametersOnly";

// * ** Call method: Method name, Array of parameters, Callback and Error handlers
Manual.callMethod("AddNumbers",[212,122],ManualMethodCall_Callback,OnPageError);
```

This code is low level as you're manually creating this object and setting the properties and then making the method call. Notice that the call can be completely dynamic as you can specify the method and server and control to call at runtime.

The [Method Proxy Callback](vfps://Topic/_1WC0XYGEI) mechanism provides a more server driven approach that sets up most of this code for you through server properties, so that's all that's left to do is make the remote method call.

This mechanism is very useful for server control development as the code can dynamically generate callback functions and parameters to call.