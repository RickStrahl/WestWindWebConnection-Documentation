This method allows creation of an empty FoxPro object that matches the properties of the schema definition in the WSDL so that this object can be used to populate values and use it as an input parameter.

This method does it's best to parse the schema definition to create a matching FoxPro object. 

Properties of simple types are created and filled with FoxPro default values (ie. "" for string, .F. for bool, 0 for numbers etc.).
  
Objects are parsed and child objects created unless llNoRecursion is passed as .T. This means child object hierarchy is automatically created for all 'object' types other than lists/arrays/collections.

Array and List values (ArrayOf in the WSDL) elements are created as empty Collections. No recursion into these collections occurs. To add items you'd create the individual type. For example:

```foxpro
loOrder= loSoap.CreateObjectFromSchema("Order")
? loOrder.OrderDate  && {}
? loOrder.OrderTotal  && 0.00
? loOrder.OrderID   && ""

* ** Add a lineitem in an WSDL array property
loDetail = loSoap.CreateObjectFromSchema("OrderDetail")
loDetail.OrderId = "220"
loDetail.OrderDate = DateTime()
loOrder.LineItems.Add( loDetail )
...
loSoap.CallWsdlMethod("SubmitOrder",lcWsdl)
```