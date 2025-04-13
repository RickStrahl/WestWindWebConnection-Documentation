Allows you to specify a method in the current page/container that is called in response to an AutoComplete callback from the client.

The method implemented needs to accept a single parameter and must return a cursor that contains at least a label and value field/property.

Specify a handler like this:

```html
<ww:wwAutoComplete runat="server" ID="txtAutoComplete" Text="" 
                              CallbackHandler="txtAutoComplete_Callback"  />
```

Then implement the method on the current page or user control class like this:

```foxpro
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
*  txtAutoComplete_Callback
* *** *** *** *** *** *** *** *** *** *** *** *** ***
FUNCTION txtAutoComplete_Callback(lcFilter)

SET PROCEDURE TO YahooStockServer additive
loServer = CREATEOBJECT("YahooStockServer")

* ** Returns a TStockSymbols cursor with id/symbol/company/exchange fields
lnCount = loServer.GetStockSymbols(lcFilter)

* ** Transform cursor for AutoComplete - label/value fields are required
SELECT symbol as value, PADR(Symbol + " - " + company,150)   as label, exchange ;
   FROM TStockSymbols ;
   INTO CURSOR TStockSymbols2

RETURN _Tally
```

Note that the label and value fields are required. Any additional fields are also passed to the client and are retrieved when the client selection occurs.