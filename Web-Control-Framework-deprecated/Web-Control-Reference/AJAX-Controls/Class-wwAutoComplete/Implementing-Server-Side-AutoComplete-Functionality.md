Server side data for the wwAutoComplete control can be served in several ways:

* Using the CallbackHandler property to point at a Page level method to return a cursor
* Using a simple Process Class Method
* Using a generic CallbackHandler Implementation
* Using any external URL to serve the data

### Using the CallbackHandler Property and firing a Page Method
The easiest way to serve data to the AutoComplete control is to use the CallbackHandler property and a Page level callback method. The method called on the Page receives a filter string of the input from the control, and the control must then return a cursor and record count, which is then formatted into the appropriate JSON and returned. It's very easy because all you have to do is implement a page method.

Start with the HTML Markup and set the CallbackHandler property:

```html
<ww:wwAutoComplete runat="server" ID="txtSymbol" 
                   CallbackHandler="txtSymbol_AutocompleteCallback" />
```

Then implement the method specified. The method has to receive a single parameter of the text in the textbox, and must return a cursor with label/value fields. It also has to return the record count of the cursor returned:

```foxpro
FUNCTION txtSymbol_AutoCompleteCallback(lcFilter)

loServer = CREATEOBJECT("YahooStockServer")
lnCount = loServer.GetStockSymbols(lcFilter)

*** Transform cursor for AutoComplete - label/value fields are required
SELECT symbol as value, PADR(Symbol + " - " + company,150)   as label, exchange ;
   FROM TStockSymbols ;
   INTO CURSOR TStockSymbols2

RETURN _Tally
```

And that's all there's to it. The control internally will process this result and return it as the appropriate JSON response to the client.

While easy to implement, this mechanism has some overhead as it runs through the Page pipeline all the way through the OnLoad() event. 

### Using a Process Class Method
If performance is important to you, you can also use a process method to implement the server side code instead. Because it doesn't go through the Page object, performance is much better, but the implementation is more low level.

Using this approach you set the ServerUrl property explicitly to the Url of your Process Method:

```html
<ww:wwAutoComplete runat="server" ID="txtSymbol" ServerUrl="SymobolAutoComplete.wwd" />
```

The Process class method is then implemented like this:

```foxpro
FUNCTION SymbolAutoComplete()

*** Manually capture the filter expression from the URL
lcFilter = Request.Params("term")

loServer = CREATEOBJECT("YahooStockServer")
lnCount = loServer.GetStockSymbols(lcFilter)

*** Transform cursor for AutoComplete - label/value fields are required
SELECT symbol as value, PADR(Symbol + " - " + company,150)   as label, exchange ;
   FROM TStockSymbols ;
   INTO CURSOR TStockSymbols2

IF lnCount = 0
   RETURN null
ENDIF

*** Manually serialize the data
loSer = CREATEOBJECT("wwJsonSerializer")
Response.ContentType = "application/json"
Response.Write(loSer.Serialize("cursor:TStockSymbols2"))
Response.End()

ENDFUNC
*   SymbolAutoCompleteHandler
```

Note that this code explicitly uses JSON serialization to encode the result. Here we use wwJsonSerializer's cursor serialization and a specialization that doesn't generate a top-level Rows property as it does by default. *cursor* produces a raw array with child objects just like the AutoComplete control requires.

You can also use a more generic [wwJsonService](vfps://Topic/_1WU1A33ET) implementation where a single process method routes requests to a separate object (or Process Class Methods) so that JSON serialization is handled in on place through the service interface. 

You can find out more about this implementing a generic handler in [Hooking up wwJsonService](vfps://Topic/_1WU1B8JKQ).