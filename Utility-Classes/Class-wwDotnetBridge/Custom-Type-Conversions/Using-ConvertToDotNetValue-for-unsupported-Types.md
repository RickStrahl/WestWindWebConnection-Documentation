[ConvertToDotNetValue()](vfps://Topic/wwDotNetBridge%3A%3AConvertToDotNetValue) provides type conversions for some .NET types that aren't directly supported by FoxPro using CAST() or automatic typing. 

Calling InvokeMethod() or SetProperty() requires that the types passed from FoxPro match the .NET method or property signature exactly or you'll get a *Method not Found* error. This is due to the fact that the indirect calling mechanism in InvokeMethod and SetProperty pass variant data that is not explicitly typed using default FoxPro typing which often doesn't line up with .NET types. To get around this all types to these methods have to be explicitly typed properly to match the .NET method signatures.

ConvertToDotNetValues() provides some basic conversions by wrapping a .NET object (variant) value that has a .NET subtype defined properly and returning this object. This object can then be used in lieu of the raw value to pass. wwDotNetBridge, then picks up the Value property and it's properly typed .NET value and uses that as the parameter instead.

```foxpro
* ** Create .NET object
loType = loBridge.CreateInstance("Westwind.WebConnection.TypePassingTests")

* ** Create ComValue structure for our Int16 parameter (value of 16)
loval = loBridge.ConvertToDotnetValue(16,"int16")

* ** Pass the ComValue structure in lieu of the unsupported parameter value
* ** to call the PassInt16 method that has a Int16 parameter
? loBridge.Invokemethod(loType,"PassInt16",loVal)
```

Behind the scenes this function uses the ComValue object to perform conversions. You can also use the ComValue object directly which is a little more verbose but provides potentially more options in the future:

```foxpro
* ** Create .NET object
loType = loBridge.CreateInstance("Westwind.WebConnection.TypePassingTests")

* ** Use ComValue object to create Int16 value
LOCAL loVal as Westwind.WebConnection.ComValue
loVal = loBridge.CreateInstance("Westwind.WebConnection.ComValue")
loVal.SetInt16(16)

* ** Pass the ComValue structure in lieu of the unsupported parameter value
* ** to call the PassInt16 method that has a Int16 parameter
? loBridge.Invokemethod(loType,"PassInt16",loVal)
```

There are a host of SetXXXX methods on the ComValue object that perform various VFP to COM conversions that CAST() can't handle.