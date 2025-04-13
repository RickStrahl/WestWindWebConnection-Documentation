The `wwRestService` class is a specialized `wwProcess` class that can be used for REST services. This class creates REST services using FoxPro method as HTTP endpoints, with methods receiving a single input parameter that is parsed from a JSON payload, and returning a FoxPro result - a value, object, collection or cursor - which is then turned into a JSON HTTP response. 

The class also provides for automatic error handling, error response results and Bearer Token Authentication.

A Rest process has these features:

* **Create a Method with simple parameter input and output**  
REST methods are implemented receive parameters that are parsed from JSON, and return values as plain FoxPro values, objects, collections, arrays or cursors. Typically you don't use Request and Response objects much, but rely on API parameters to pass in data and simply return values/objects.

* **JSON parsing and JSON and XML Output**  
For each request a single value/object can be passed as a JSON object from the client and get mapped to the endpoint input parameter. Any FoxPro return values from the endpoint method are by default turned to JSON and optionally to XML. 

* **Use Accept HTTP Header from Client to specify JSON or XML**  
Use the Accept: HTTP header to specify what format to return (Accept: application/json, Accept: text/xml). Note that JavaScript libraries like jQuery and Angular automatically POST data as application/json so you get JSON input typically.

* **Continue to use the Request Object to get other Request info**  
You can still use Request.QueryString() and Request.Form() to parse non-JSON input data from URLs and it's also possible to return Response.Write() results by setting Service.IsRawResponse = .T. which doesn't serialize the result or tries to set the output content type.

* **[InitTokenSession()](VFPS://Topic/_6R60ZS1WR) for a basic Bearer Token Generation and Tracking**  
You can use Web Connection's `wwSession` object via the `InitTokenSession()` method to create, track and validate Authorization Bearer tokens and make them accessible via the `oSession` instance.

* **[Authenticate()](VFPS://Topic/_6R619BPU9) for tying Bearer Tokens to wwUserSecurity User Records**  
In addition to wwSession support you can also map a Bearer Token to a user records using `wwUserSecurity`. By using the `Authenticate()` method to validate and authorize users, sessions and users are automatically linked in the same way they are for standard wwProcess HTML applications.