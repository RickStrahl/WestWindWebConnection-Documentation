You can create a simple JSON service in a single Process class method

```foxpro
FUNCTION JsonCallbacks

*** Instantiate or reference the class that will will handle the method call
*** Here I just use the Process class itself which will handle the call
loTarget= CREATEOBJECT("AjaxCallbacks")  && Class that handles callback

*** You can also use THIS and use the methods on the process class
* loTarget = THIS

*** Instanantiate the service
loService = CREATEOBJECT("wwJsonService")

*** Return a JSON Response
Response.ContentType = "application/json"

*** Write out the result from CallMethod which returns the JSON for the method
*** or a JSON Error object if the call failed
lcResult = loService.CallMethod(Request,loTarget)

*** Return an error response so jQuery's error handler kicks in
*** Note: Result is still a JSON response
IF loService.ErrorOccurred
   Response.Status = "500 Server Error"
ENDIF

Response.Write(lcResult)
RETURN
```

You can then implement a simple class that methods that returns simple values and objects as JSON results:

```foxpro
*** Class that has simple methods that act as callback handlers
*** Results from this are automatically JSON encoded
DEFINE CLASS AjaxCallbacks as Custom

*** This is the target process method
FUNCTION HelloWorld(lcName)
RETURN "Hello " + lcName + ". Time is: " + Time()

FUNCTION Add(lnNumber, lnNumber)
RETURN lnNumber + lnNumber

FUNCTION GetCursor()
select * from tt_cust into cursor TQuery order by company
RETURN "cursor:TQuery"     && Serialized as a JSON array

ENDDEFINE
```

Each of these methods is now accessible for JSON callbacks with this url (assuming a .wwd scriptmap):

* JsonCallbacks.wwd?Method=HelloWorld
* JsonCallbacks.wwd?Method=Add
* JsonCallbacks.wwd?Method=GetCursor


### Using ajaxJson() to call the server method
You can use [ajaxJson()](VFPS://Topic/_3RH0Y8UH5) method in ww.jquery.js to call the server as long as you can limit yourself to a single parameter - which may be an object or an array.  Note that using the client side ajaxJson() function allows you to pass exactly one parameter to the server (use null for none) and you need to make sure your method accepts one parameter. If you need to pass more than one parameter **you can pass an object** that holds the parameters. 

For example in JavaScript you can declare:

```javascript
var parms = { Name: "Rick", Rate: 150.00 };
ajaxJson("jsoncallbacks.wwd?Method=PassObject",
                parms,function(result)
                { alert(result); }, onPageError);
```

and you can then pass this parameter. The server code will then receive the object as a parameter and can extract the individual parameters. Objects can be nested as well. Your result is the result from the method call on the server encoded in JSON format. You can return simple values, objects, hierarchical objects and even cursors (return "cursor:CursorName").

### Using jquery.ajax
You don't have to use the Web Connection client code either - it's possible to use other libraries to access this functionality - you can use GET operations or POST operations and simply passing data via query strings or POST data. For example, to use jQuery to call the same code above:

```javascript
jQuery.ajax( { url: "jsoncallbacks.wwd?Method=Helloworld",
               dataType: "json",
               success: function(result) { alert(result); },
               error: onPageError
} );
```

This works just as well, although you loose the capability to pass parameters (unless you POST and JSON encode them on your own). jQuery.ajax for example allows you to serialize form fields automatically and send that along with the request. To pass JSON parameters with jQuery you'll need to use a JSON serializer. But it's much easier to use either the ajaxCallMethod() and ajaxJson() functions which wrapper the jquery.ajax() function internally.

The point though is that you can call into a JSON service configured many different ways and it doesn't have to be from Web Connection client script code.