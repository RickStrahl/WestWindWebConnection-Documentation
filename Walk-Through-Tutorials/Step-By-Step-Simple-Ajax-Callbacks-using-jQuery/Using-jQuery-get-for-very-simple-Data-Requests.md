The simplest jQuery function you can use to retrieve content from a server is [$.get()](https://api.jquery.com/jquery.get/). 

You provide a URL and it'll return a promise object with `.then()`, `.catch()` methods for success and error responses respectively.

The following requires `jquery` and `ww.jquery.js` which are part of every Web Connection project.

You'll want to make sure you add the following to your HTML document:

Assume you have some fields on a Web Connection page like this:

```html
<div class="card card-default">
    <div class="card-header">Simple jquery.get()</div>
    <div class="card-body">
        <p>
            This simple example sends a value to the server and returns
            a result value to place in the text box below.
        </p>
        
        <div class="form-group">
            <a id="lnkAjaxCallback" href="#0">What's the Answer?</a>
        </div>

        <div class="form-group">
            <label class="control-label" for="basicinput">Result</label>
            <input type="text" id="txtResult" name="txtResult" class="form-control" />                
        </div>
    </div>        
</div>

<!-- AT THE BOTTOM OF THE DOCUMENT -->
    <script src="/lib/jquery/dist/jquery.min.js"></script>
    <script src="/scripts/jquery.min.js"></script>
</body>
```

There are two items of interest:

* A link `lnkAjaxCallback` which when clicked initiates the Ajax Request
* A textbox `txtResult` which receives the result

### Server Code
To serve the data you'll need to have some request on the server that can return data to you. The easiest is to create a new method on your process class and have it return a **raw response** that returns just data as string.

```foxpro
FUNCTION SimpleAjaxCallback()

*** Get 'parameters' off the query string
lcId = Request.QueryString("id")

***  do something with the data

*** Create your string output
Response.ContentType = "text/plain"
Response.Write("The answer is 42. Time is " + TIME())
ENDFUNC
```

This request now becomes available as `SimpleAjaxCallback.wwd` where `.wwd` is my script map extension for this process. Use your extension as appropriate.

### Script Code
To call this new server function via Ajax call, we can hook the click on the link and then fire the Ajax request with `$.get()`:

```html  
<!-- <script src="~/lib/jquery/dist/jquery.min.js"></script> -->

<script>
$("#lnkAjaxCallback").click(function() {
    $.get("SimpleAjaxCallback.wwd?t=ajax&id=10")          
        .then(function(res) {
            $("#txtResult").val(res);
            
            // For labels
            // $("#txtMessage").text(res);
        });
} );
</script> 
```

Now when you click the link you will see the text box filled with the result value from the server.


### String Only?
Yes - HTTP results are always strings so even if you need to return some other value you'll need to capture that value as a string and convert it on both the server when sending and when receiving the value.

This is where Services are easier because they automatically handle converting values from string to proper typed values. Using Services - which use JSON - you can pass non string value and even objects, collections and cursors from the server to the client and the client receives these values in their proper types.

### Using JSON
If you want to do this manually on your own you can also manually use JSON parsing on the server and client.

On the server imagine you want to return an object:

```foxpro
FUNCTION ReturnAjaxObject()

*** Do your processing

*** set up return value
loObject = CREATEOBJECT("empty")
ADDPROPERTY(loObject,"message","Hello from the server")
ADDPROPERTY(loObject,"time",DATETIME())

loSer = CREATEOBJECT("wwJsonSerializer")
lcJson = loSer.Serialize(loObject)

Response.ContentType = "application/json"
Response.Write(lcJson)
ENDFUNC
```

jQuery.get automatically converts any content of type `application/json` into a value or object so the client code is fairly simple:

```javascript
$("#lnkAjaxObject").click(function() {
    $.get("ReturnAjaxObject.wwd?t=ajax&id=10")          
        .then(function(result) {                        
            // JSON doesn't parse date values
            var time = new Date(result.time).toLocaleTimeString();
            $("#txtObjectResult").val( result.message + " at " + time);
        });
} );
```

> Note that JSON parsing in JavaScript doesn't convert date values automatically. Dates are serialized as **ISO-8601 strings** (ie. `time: "2020-01-13T20:16:01Z"`) which are not automatically deserialized into dates. The deserialized value is still a string. However, you can turn this date into a string easily with `new Date(obj.time)` as the example does above.

If you do more than a few requests this way I again would recommend you [create a separate REST Service](VFPS://Topic/_4IU1EV8PM) or REST Callback Handler instead, which automates the serialization for you. It also handles input parameters, which aren't covered here. With services it's easy to pass both data to and from the server using JSON.