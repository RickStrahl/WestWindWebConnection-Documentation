Although the AJAX features in Web Connection are primarily geared at the Web Control Framework which makes working with AJAX very easy, you can also use the various AJAX features - and JSON callbacks in particular - with plain old wwProcess methods. 

In particular there's a wwJsonService class which is basically an RPC JSON service implementation that can be very easily hooked up to any single wwProcess method. The service class lets you route client requests to a specific method of an object that you specify in the service setup. The class handles all input parameter conversion, the actual method call, error handling and returning of a JSON string response.

You can use the wwJsonService features in conjunction with the ww.jquery.js client library, but it's not required. Because wwJsonService takes input in the form of AjaxMethodCallback style parameters, a raw JSON Post object or no particular POST format at all it's up to you to decide how to pass parameters.

### Implementing a JSON Service Handler in a Process Method
The first step is to set up the service on the server. A 'service' in this context is really nothing more than an instance of wwJsonService plus a target object on which methods are executed. I recommend you use a separate object for the actual method implementations, but you can also point at THIS which here would be the wwProcess subclass.

Here's an example process method implementation which is pretty much cut and paste except for the object instance that holds the actual methods to call:

```foxpro
************************************************************************
*  JsonCallbacks
****************************************
***  Function: Demonstrates how to create a generic wwJSONService
***            end point in a process class.
***            This method uses a separate object to handle the
***            actual method calls but you could also use THIS
***            as the target object to directly execute Process
***            Methods.
***
***            ajaxJson("JSONCallbacks.wwd","MyServiceMethod",["parm1",10,new Date()]);
***      Pass: 
***    Return:
************************************************************************
FUNCTION JsonCallbacks()

*** You don't have to create a new object - you can also use THIS
*** But using a separate object ensures you only expose methods
*** you care to expose rather than Process methods
lotarget= CREATEOBJECT("AjaxCallbacks")   && THIS

*** Instantiate the Json Service
loService = CREATEOBJECT("wwJsonService")

*** Response is ALWAYS JSON unless there's a hard server error 
Response.ContentType = "application/x-javascript"

*** Simply call the specified method - 
*** The service will pull out method parameters from REQUEST
*** Note the service will handle errors internally and return
*** a JSON error object 
lcJSON = loService.CallMethod(Request,loTarget)

*** Write out the result from CallMethod which returns the JSON for the method
*** or a JSON Error object if the call failed
Response.Write( lcJSON )
Response.End()

RETURN
ENDFUNC
*   JsonCallbacks
```

That's all there's to the service implementation. You create an instance of wwJsonService and you then call the CallMethod() method to actual route a request to the service handler. The service is passed a Web Connection Request object, the target that will handle the method calls - our AjaxCallbacks instance in this case.

### Implementing the Service Handler Class
Above I'm using an AjaxCallbacks class to handle the requests, so we need to implement this class. The class is nothing special - it doesn't need to inherit from anything and simply should implement methods that are called from Ajax callbacks. Depending on how you plan on calling the methods the parameter signatures may vary. If you use the ww.jquery.js 'method' calling features you can pass any number of parameters from the client to each method. If you pass a raw JSON objects as a POST object you can accept exactly one parameter. If you don't want to pass anything or just pass up free form POST data don't accept any parameters and simply read the POST data using Request.Form.

Here's an example implementation of a JSON callback handler class:

```foxpro
*************************************************************
DEFINE CLASS AjaxCallbacks AS Custom
*************************************************************

************************************************************************
*  HelloWorld
****************************************
FUNCTION Helloworld(lcName) 
lcName = lcName.Name
RETURN "Hello " + lcName + ". Time is: " + TIME()

************************************************************************
*  GetServerTime
****************************************
FUNCTION GetServerTime()
RETURN DATETIME()


************************************************************************
*  HelloWorldObject
****************************************
FUNCTION HelloWorldObject(lcName, loResult)

loObject = CREATEOBJECT("EMPTY")
ADDPROPERTY(loObject,"x",10)
ADDPROPERTY(loObject,"y",15)
ADDPROPERTY(loObject,"Name",loResult.Name + CRLF + loResult.Address.Street)
ADDPROPERTY(loObject,"date",DATETIME())

RETURN loObject

************************************************************************
*  GetCustomerList
****************************************
FUNCTION GetCustomerList

SELECT Company,CareOf,Pk FROM TT_Cust ;
	ORDER BY COMPANY INTO CURSOR TQUERY

*** Return the cursor as a result. NOTE we have to leave it open
*** for the parser to create it. The parser wiil close it
RETURN "cursor:TQuery"

************************************************************************
*  GetStockQUote
****************************************
FUNCTION GetStockQuote(lcSymbol)

LOCAL loServer as YahooStockServer
loServer = CREATEOBJECT("YahooStockServer")

LOCAL loQUote as YahooStockQuote
loQuote = loServer.GetStockQuote(lcSymbol)
IF ISNULL(loQuote)
	*** Force error to be marshalled to client
	ERROR loQuote.cErrorMsg
ENDIF	

RETURN loQuote
ENDFUNC

ENDDEFINE
```

This example is provided as part of wwDemo so you can experiment around with this. As you can see these methods just are plain old methods that take input parameters and return values. A number of different types of values are returned and the JSON encoding preserves the types back to the client.

The service is now ready to receive requests. In this case, any request to JsonCallbacks.wwd will now route to this service.

### Calling the Service from JavaScript
There are lot of ways that you can call this service now. The easiest is by using the [ajaxCallMethod](vfps://Topic/ajaxCallMethod) function in ww.jquery.js. In order to use ww.jquery.js 

```html
<head>
	<script src="scripts/jquery.min.js" type="text/javascript" ></script>
	<script src="scripts/ww.jquery.min.js" type="text/javascript" ></script>
      <title>Ajax JSON Method Callbacks</title>
</head>
```

Then inside of a script tag of the page you can now call the service. In this case I'm going to call the GetStockQuote method which returns an object:

```html
<script>
function getStockQuote()
{
    var stock = $("#txtStock").val();

    // call the generic callback method wrapper function from ww.jquery.js
    // interfaces with wwJsonService class on server
    ajaxCallMethod("jsonCallbacks.wwd","GetStockQuote",[stock],
                    function(result) {                                                             
				  alert(result.lastprice + " " + result.lastquotetime);                        
                     },onPageError);
} 
</script>
```

ajaxCallmethod() is a quick and dirty wrapper around the AjaxCallbackMethod object that assigns the URL and input parameters and fixes them up in the way that the JSON service expects it. You pass in the Url to call (our ProcessMethod JsonCallbacks.wwd in this case), the method in the service, and an array of parameters (or []) for no parameters. Finally there's a callback function that is called with the result data on success or an error function that is called on error (onPageError is a stock method that shows an alert box with the error).

But you don't need to use this high level functionality. If you'd rather just use plain old jQuery you can do that too:

```javascript
function getStockQuotes() {
    var symbols = { quote1: "MSFT",
        quote2: "LDK",
        quote3: "INTC"
    };

        $.ajax({ url: "jsonCallbacks.wwd?Method=GetStockQuotes",
            type: "POST",
            dataType: "json",
            data: symbols,
            success: function(quotes) {            
                var output = "";
                for (var i = 0; i < quotes.length; i++) {
                    var quote = quotes[i];
                    output += quote.company + " (" + quote.lastprice + ")<hr/>";
                }
                $("#divStockResult").addClass("errordisplay").html(output).fadeIn();
            },
            error: onPageError
        });
        
        return;    
}
```

This code use the static jQuery .ajax() function. There are other methods (getJSON(), post() etc.) but ajax is the only one that gives enough control of callbacks to pass and retrieve a JSON response. Not I'm using dataType json to automaticall parse the result from JSON into an object, and I'm passing the symbols as plain POST data (jQuery converts objects into POST key value pairs).

Here's the FoxPro method code on the server in the AjaxCallbacks class:

```foxpro
FUNCTION GetStockQuotes(loSymbolList)
LOCAL loServer as YahooStockServer
loServer = CREATEOBJECT("YahooStockServer")

loQuotes = CREATEOBJECT("wwCollection")

FOR lnX = 1 TO 99
	lcQuote = Request.Form( "quote" + TRANSFORM(lnX) )
	
	IF EMPTY(lcQuote)
		EXIT
	ENDIF

	loQuote = loServer.GetStockQuote(lcQuote)	
	IF ISNULL(loQuote)
		CONTINUE
	ENDIF
	loQuotes.Add(loQuote)
ENDFOR

RETURN loQuotes
ENDFUNC
```

This code picks up the symbols sent from the client as POST data one value at a time. The value is then used to look up a stock quote. It's added to a collection which is then turned into a JSON array and returned back to the client.