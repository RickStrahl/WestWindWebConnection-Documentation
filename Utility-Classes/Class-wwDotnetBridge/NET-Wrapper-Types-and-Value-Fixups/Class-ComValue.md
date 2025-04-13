`ComValue` is a wwDotnetBridge .NET helper class that wraps problem .NET types that **cannot be accessed in FoxPro due to COM Type Limitations**. COM doesn't support many .NET types and this class allows you to use them with this wrapper object that can be passed and returned to and from the explicit invocation methods like `InvokeMethod()`, `SetProperty()`, `GetProperty()`, which know how to fix up the `ComValue` structure into real .NET types and vice versa.

This applies both to inbound processing as a method parameter or property set value, as well as on return values. For example, the `Long` type cannot be passed over COM. If a method requires a `Long` parameter, you can create a `ComValue` instance and use `.SetLong(lnValue)` to pass the value to `InvokeMethod()`. When the call returns and it also returns a `Long`, `InvokeMethod()` automatically returns a `ComValue` object from which you can retrieve the value `.GetLong()` (or `.GetInt64()`).

> **Important:** `ComValue` fixup works only with the `wwDotnetBridge` intrinsic methods. 
It's important to understand that it's **wwDotnetBridge** that understands `ComValue` instances, not .NET so you can only pass or receive a `ComValue` through the *indirect access methods*, never by accessing the .NET COM instance directly.

Examples of unsupported types include:

* `Long`, `Single`, `Decimal` number types
* `Guid`, `Byte`, `DbNull`, `char`
* Any Value type (based on `struct` in C#)
* Enum Values
* Access to any Generic Value or Type

That's a pretty wide swath of types that are inaccessible via direct instance COM access, but with the help of the `ComValue` class it's still possible to access these types in FoxPro.

### How it works
`ComValue` works by creating a .NET wrapper object with a `Value` property that holds the actual .NET value and methods that allow setting and retrieving that value - or a translation thereof - in FoxPro. The `Value` is stored in .NET and **is never passed directly to FoxPro**. Instead you pass or receive a `ComValue` instance that **contains the Value** and has conversion routines that allow convert the value between .NET and FoxPro types.

### Automatic ComValues
ComValue results are automatically returned for known problem types with:

* `GetProperty()`
* `InvokeMethod()` result values
 
For example, if you call a method that returns a `Guid` value, `GetProperty()` or `InvokeMethod()` return a `ComValue` object and you can call `GetGuid()` to retrieve the Guid as a string.

```foxpro
loGuid = loBridge.InvokeMethod(loObj,"GetGuid") 
* Get Guid from ComValue
lcGuid = loGuid.GetGuid()
```

You can **pass ComValue objects** when using these methods:

* `SetProperty()`
* `InvokeMethod()` parameters
* `CreateInstance()` constructor parameters
* `ComArray.AddItem()`

For these methods you create a `ComValue` instance and set the value and then pass that to one of the above methods.

```foxpro
lcGuid = GetAGuidStringFromSomewhere()
loGuid = loBridge.CreateValue()
loGuid.SetGuid(lcGuid)

llResult = loBridge.InvokeMethod(loObj,"SetGuid",loGuid)
```

### Simple type conversion:

```foxpro
*** Create .NET Object instance
loNet = loBridge.CreateInstance("MyApp.MyNetObject")

*** Convert the 'unsupported' parameter type
LOCAL loVal as Westwind.WebConnection.ComValue
loVal = loBridge.CreateComValue()
loVal.SetInt16(11)

*** Call method that takes Int16 parameter
loBridge.InvokeMethod(loNet,"PassInt16",loVal)
```

### ComValue caching for Method and Property Invocation 
This class also supports setting a ComValue from properties and method results. This is useful if you have a method or property that uses a type inaccessible via COM (like strongly typed or subclassed dataset objects for example). In this case you can call the SetValueXXX methods to fill the ComValue structure and then use this ComValue in InvokeMethod, SetProperty calls which automatically pick up this ComValue object's underlying .NET type.

```foxpro
*** Create an array of parameters (ComArray instance)
loParms = loBridge.CreateArray("System.Object")
loParms.AddItem("Username")
loParms.AddItem("Password")
loParms.AddItem("Error Message")

*** Create a ComValue structure to hold the result: a DataSet
LOCAL loValue as Westwind.WebConnection.ComValue
loValue = loBridge.CreateComValue()

*** Invoke the method and store the result on the ComValue structure
*** Result from this method is DataSet which can't be marshalled properly over COM
? loValue.SetValueFromInvokeMethod(loService,"Login",loParms)

*** This is your raw DataSet
*? loValue.Value   && direct access won't work  because it won't marshal

*** Now call a method that requires the DataSet parameter
loBridge.InvokeMethod(loService,"AcceptDataSet",loValue)
```

The jist of this is that the DataSet result is never passed through FoxPro code, but is stored in ComValue and then that ComValue is used as a parameter in the InvokeMethod call. All indirect execution methods (InvokeMethod,SetProperty etc.) understand ComValue and use the Value property for the parameter provided.

For more detailed information check out this blog post:

* [wwDotnetBridge: Getting and Setting COM Unsupported Values with ComValue](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=955)