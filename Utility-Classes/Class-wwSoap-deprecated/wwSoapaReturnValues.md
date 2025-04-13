On the client side the array that holds the return values from a method call. On the server side it holds the return values that are to be returned by CreateSoapResponseXML.

**Client:**  
You can use this method to return additional parameters beyond the return value - typically ByRef parameters:

<pre>oSOAP.AddParameter("name","rick strahl"
lvResult = oSoap.CallMethod("GetPendingEntries")

* ** Show by ref parameters if any
FOR x=2 to  oSoap.nReturnValueCount
  ? oSOAP.aReturnValues[x,1],oSoap.aReturnValues[x,2]
ENDFOR

* ** Get return value by name
lvreturn = oSOAP.GetParameter("return",.t.)
lvValue = oSOAP.GetParameter("name",.T.)  && .T. = ReturnValues
</pre>