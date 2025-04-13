This method is the raw method that initiates method callback to a AjaxMethodCallback control or a wwCallbackHandler derived Http handler on the server routing to methods marked with [CallbackMethod] attributes on the target object.

```cs
// * ** Create the callback class and pass the ID of the Callback control
var Manual = new AjaxMethodCallback();

Manual.serverUrl = "SimpleMethodcallbacks.aspx";  // optional - default to current page

// * ** Optionally specify a server control to route the callback to
// Manual.targetControlId = "MyServerControl"

// * ** You can specify how data is posted back. Get, Post, PostNoViewState and parameters only (default)
Manual.postbackMode = "PostMethodParametersOnly";

// * ** Call method: Method name, Array of parameters, Callback and Error handlers
Manual.callMethod("HelloWorld", [$("#txtName").val()],
                function(result) {
                    alert(result);
                },
                function(error) {
                    alert("Error: " + error.message);
                });
```

Parameters are passed in an array and should be passed as JavaScript values/objects which are turned into JSON arguments.