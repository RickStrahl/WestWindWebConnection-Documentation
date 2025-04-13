This function provides an easy way to call an HTTP url that takes a JSON input and returns a JSON response. The method automatically parses a parameter object to JSON and result JSON into a result object or value.

You can pass a value or object that is turned into JSON which by default is POST'ed to the server. If no parameter is provide the request defaults to a GET operation. You can override the verb via the `options.verb` parameter. You can also use the `options.noPostEncoding` option to prevent JSON encoding of parameters.

The result is handled either via the two callback parameters for success and error respectively:

**JSON Object or value (just pass a value):**

```javascript
function CallServiceWithPromise()
{
    ajaxJson("timetrakkerservice.svc/LoadCustomer",
			{Pk:12,Entered:new Date()},{verb: PUT},
    		function (result)  { // parsed object instance
    		    alert( result.d.Company);
    		    alert( result.d.ProjectEntities[1].CustomerEntity.LastName);
    		},
    		function(error) {  // error object
                alert("Error: " + error.message); // parsed error
            });
```

**Raw POST string:**

```javascript
function CallServiceWithCallbacks()
{
    ajaxJson("timetrakkerservice.svc/SaveCustomer",
			"Name=Rick&Company=West+Wind",
			Callback,onError,
			{ contentType: "application/x-www-urlencoded", noPostEncoding: true});
}
function Callback(result)
{
    alert( result.d.Company);
    alert( result.d.ProjectEntities[1].CustomerEntity.LastName);
}
function onError(error)
{
    alert("Error: " + error.message);
}
```

> `ajaxJson()` automatically uses `application/json` by default for the `Content-Type` and `Accept` headers in the request. If you need to send or receive data of a different type be sure to explicitly set the `Headers`

Note that if passing a string you should pass the content type explicitly - otherwise the string will be posted as JSON. 

If you are calling a CallbackHandler or AjaxMethodCallback control on the server use the [ajaxCallMethod](vfps://Topic/ajaxCallMethod) function instead.

### Setting custom headers
If you need to send custom headers you can you use the `options` parameter. There are a number of common preset properties you can set on this options object that affect the HTTP object's behavior, or you can directly add `headers` as key value pairs:

```javascript
ajaxJson("/timetrakkerservice.svc/SaveCustomer",
	"Name=Rick&Company=West+Wind", // url-encoded form data
	Callback,onError,
	{  // options
	
	  // built-in props
	  contentType: "application/x-www-urlencoded", 
	  noPostEncoding: true,
	  
	  // explicit custom headers
	  headers: {
	      "security-header": "custom value",
	      "keep-alive": -1
	  }
    });
```