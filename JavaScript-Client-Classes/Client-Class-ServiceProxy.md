The ServiceProxy class lets you call Microsoft ASP.NET AJAX based WCF or ASMX JSON services. It handles JSON serialization and deserialization as well as URL and parameter fixups for data passed.

The key feature of this class is the invoke method which is used to invoke a server side method and call the specified callback handler with the result. 

Server method Interface signature (WCF):
```cs
[OperationContract]          
[WebInvoke(Method="POST",
   BodyStyle=WebMessageBodyStyle.Wrapped,
   ResponseFormat=WebMessageFormat.Json)]
StockQuote GetStockQuote(string symbol);
```

JavaScript client code to call it.

```javascript
// Create a static instance (global so it can be reused)
var serviceUrl = "JsonStockService.svc/"; // specify service 'base' url
var proxy = new serviceProxy(serviceUrl);

function GetStockQuote()
{       
    var symbol = $("#txtSymbol").val();            
    if (!symbol)
    {
        showStatus("Please provide a symbol value.",5000);
        return;
    }    
    
    // pass single symbol parameter
    proxy.invoke("GetStockQuote",{"symbol":symbol},
        function (result)  // returns a quote object
        {   
            if (!result)
            {
                showStatus("Invalid Stock Symbol");
                return;
            }
            showStockProgress(true);
            
            
            $("#StockName").text( result.Company + " (" + result.Symbol + ")" ) ;
            $("#LastPrice").text(result.LastPrice.toFixed(2));
            // additional ui assignments
        },
        function(error) { alert(error.message); },
        false); 
}
```