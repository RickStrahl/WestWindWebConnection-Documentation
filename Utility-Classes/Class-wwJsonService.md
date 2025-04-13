This class is a self contained JSON method handler. It takes a REQUEST object as input and based on the request input routes the request to a method that is called optionally with parameters specified as part of the request. 

You can also use a pre-made process classes called wwRestProcess that internally implements sets up and calls wwJsonService, so any methods are automatically mapped to JSON endpoints.

The class is fully self contained and handles the entire process of parsing parameters, calling the method, handling errors, and returning a JSON result string. All results including code errors always return a JSON result.

The class accepts Web input in 3 different ways:

* **POST parameters using ajaxMethodCallback() or the wwAjaxMethodCallback control**  
This is the preferred way to call the service as it lets you basically call any method with any signature passing in multiple parameters. Parameters are expected to be JSON encoded. Use either the AjaxMethodCallback class or ajaxCallMethod() function in ww.jquery.js. Both of these are practically one liners.
*Content Type Expected:*  application/x-urlencoded
*Post Variables expected:*  CallbackMethod,CallbackParmCount,Parm1,Parm2..ParmN

* **POST a JSON value or object**  
This mechanism allows you to effectively pass a single JSON encoded value/object to a method. The method called should take a single value/object parameter. Although a single parameter seems very limiting remember that you can easily encode multiple values into a wrapper object (  { myParm1: value, myParm2: value } ). The server method should be able to pick up those child objects and values as needed.
*Content Type Expected:*  Content-Type set to application/x-javascript or application/json
*Querystring Value expected:* Method

* **No PostData or No CallbackMethod POST value**  
Allows you to call the method and pass no data to it directly. You can still send POST data, but that data is not parsed or set in anyway. The method called then needs to pick out any 'parameters' or direction based on POST or QueryString data the request provides.
*Querystring Value expected:* Method

* **JSONP Support**  
You can also make JSONP callbacks by specifying a callback= querystring parameter to specify the callback method fired on the client by a JSONP capable client. JSONP repsonses simply wrap the resulting JSON data into a function call with the name of the callback query string value.


This class allows calling a method and returning JSON data from the result either using the client AjaxMethodCallback class which uses POST parameters, or by passing a single JSON parameter from the client with content-type of application/x-javascript.

This class can be implemented as a plain Process method in Web Connection with very little code. This class handles all aspects of request parsing and output generation and acts as a proxy to call a method on an object which means it can route incoming requests to any method.