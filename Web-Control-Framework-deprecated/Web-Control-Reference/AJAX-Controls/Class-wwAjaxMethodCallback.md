The wwAjaxMethodCallback class lets client side JavaScript code call FoxPro server side methods on objects and return the result as JSON enabled objects.

This simple mechanism provides an easy to use and efficient mechanism for JavaScript code to use the FoxPro server application as a data server that executes logic and then returns results to the client. JSON encoding of the result data allows for complex data to be returned from the server which includes simple types, simple and complex objects, FoxPro cursors (represented as object arrays), and arrays.

This class is mapped to the client side wwAjaxMethodCallback class which is automated by the server control to perform the client side callbacks.

To initiate a client side callback to the server:

```html
<ww:wwAjaxMethodCallback ID="Proxy" runat="server" 
                        PostBackMode="Post"
                        PostBackFormName="form1"
                        ScriptLocationType="WebResource"  
                        AllowedPageMethods="Helloworld,AddNumbers,GetDeveloperList,GetDeveloper,SaveDeveloper">
</ww:wwAjaxMethodCallback>
```

Client side code can then call server methods with Proxy.callMethod where Proxy is the ID of the control:

```cs
<script type="text/javascript">
    function HelloWorld()
    {
        Proxy.callMethod('HelloWorld',[document.getElementById('txtSayHello').value],Helloworld_Callback,OnError)
    }
    function Helloworld_Callback(Result)
    {
        // * ** Update element in the document with jQuery
        $('lblHelloWorldResult').html(Result);
    }
</script>
```

On the server side the implementation simply implements the Helloworld method (and any others) on the form as methods.  The default target object is the Web Control Page the control lives on, but optionally the control allows routing requests to any other object via the TargetObject property.

Callbacks handled by this control can return also JSONP responses by specifying a *callback=* query string parameter. You can use $.getJSON("ajaxrequest.wwd?callback=?") which jQuery to return remote data for example.

[Use and Operation](vfps://Topic/_1Q1040S1T)