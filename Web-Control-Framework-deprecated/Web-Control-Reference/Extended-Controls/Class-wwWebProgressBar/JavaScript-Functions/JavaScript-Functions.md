The wwWebProgressBar control works both with client side and server side code and in order to take full advantage of it you'll want to use client script code to communicate with the server to retrieve information.

There are a few client side functions that are described here.

### Embedded AJAX control
The control also loads an embedded wwWebAjax control and this control is accessible at any time by the following naming scheme:

<controlId>_Ajax

So to make a Page callback to a method called MyMethod with 2 parameters ("Parm1",2) when the ProgressBar is on the page you can simply use code like this:

```cs
CallMethod("GetProgress","MyMethod","Parm1",2,GetProgressCallback);
...
function GetProgressCallback(Result)
{
	alert(Result);
}
```

The callback function then receives the result from the callback which can include tables and objects passed from the server.