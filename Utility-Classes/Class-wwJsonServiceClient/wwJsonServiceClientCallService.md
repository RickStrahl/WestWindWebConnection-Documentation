Calls a REST service that returns and optionally expects JSON data. The JSON data is returned as a FoxPro simple value, object or collection.

### Parameter Inputs
To call a service you specify the HTTP Verb, plus any optional data to send to the service. You can only pass a single value that is serialized to JSON, but this value can be an object that contains many nested sub-objects or collections. REST services in general accept only a single data object which can contain mutliple sub-objects that act as parameters. How this is set up depends on the service however.

### Return Values
This method expects the service to return a JSON response (`application/json` content type) which is turned into a FoxPro value, object or collection that is returned as part of this method.

> #### @icon-warning Errors always return a Response Value: Check .lError!
> `CallService()` **always returns some sort of response** - either a string of the raw HTTP buffer, or if the data is `application/json` content, the deserialized JSON object. This happens **even if an error occurred** and the response contains an error.
>
> So if you get something like a `401 - Not Authorized` response and the server sends a JSON object response with error info, you get the error object back. IOW, the response won't be null or empty, but that object.
> 
> The bottom line is: To check for success of a request, You **should always check the `.lError` flag** and `.cErrorMsg`  to ascertain whether the request succeeded.