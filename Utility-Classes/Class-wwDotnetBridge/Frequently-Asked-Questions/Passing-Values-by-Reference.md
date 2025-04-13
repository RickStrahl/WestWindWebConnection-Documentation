On occasion you'll find .NET APIs that pass parameters by reference. Thankfully by ref parameters are rare since it's much easier to return objects then return values via parameters, but nevertheless there are a few APIs that require `ref` or `out` parameters that are updated by the .NET method code1.

Making these calls is not quite obvious with wwDotnetBridge. There are essentially two ways that you can make these calls:

* Using direct COM Access of .NET objects with @ FoxPro style ref parameters
* Using `ComValue` Parameters in InvokeMethod/InvokeStaticMethod calls
* Passing Arrays or Collections by Ref requires a `ComValue` wrapping a `ComArray`

### Direct Call Syntax
You can pass and retrieve reference parameters in .NET easily if you're using direct call syntax.
Assume the following function signature:


```csharp
public void PassByReference(ref int intValue, string stringValue, ref decimal decimalValue)
{
    intValue = intValue*2;
    decimalValue = decimalValue*2;
    stringValue += " Updated!";
}
```

And you can easily call it with code like the following:


```foxpro
loNet = loBridge.Createinstance("Westwind.WebConnection.TypePassingTests")
lnInt = 10
lnDecimal = 5.22
lcString = "Hello World."
loNet.PassByReference(@lnInt,lcString,@lnDecimal)
? lnInt,lnDecimal,lcString
```

Note that I'm using direct access of the function I am calling -  `loNet.PassByReference()` - and COM handles mapping the ref parameters automatically.

This works because FoxPro and COM work together to update the values.


### InvokeMethod() and InvokeMethodStatic() require `ComValue` Parameters
Direct calling allows for FoxPro * ref syntax. But the same does not work with wwDotnetBridge indirect call syntax. The following **does not work**:

```foxpro
loNet = loBridge.CreateInstance("Westwind.WebConnection.TypePassingTests")
lnInt = 10
lnDecimal = 5.22
lcString = "Hello World."
loNet.InvokeMethod(loNet,"PassByReference",@lnInt,lcString,@lnDecimal)
? lnInt,lnDecimal,lcString
```

The reason this doesn't work is that indirect calls pass the parameters through 2 additional API layers and in that process the ByRef linkage is lost - the original values are potentially translated and updated and may end up being of different types than were passed in. For this reason FoxPro @ ref syntax does not work.

To get around this you can use the `ComValue` class to hold your `ref` or `out` parameters. `ComValue` parameters pass their value to a function and also receive that value back on function call completion effectively providing the semblance of ByRef parameters. The syntax to do this is a little more verbose.

Assume you have a method like this:

```csharp
public void PassByReference(ref int intValue, ref string stringValue, ref decimal decimalValue)
{
    intValue = intValue*2;
    decimalValue = decimalValue*2;
    stringValue += " Updated!"; 
}
```

Note that 2 of the parameters that are numeric are passed by reference.

To call this method from FoxPro looks like this:

```foxpro
LOCAL loNet as Westwind.WebConnection.TypePassingTests
loNet = loBridge.Createinstance("Westwind.WebConnection.TypePassingTests")

*** Pass parameters by Reference

*** Create ComValue objects for each parameter
loInt = loBridge.CreateComValue()
loInt.Value = INT(10)

loDecimal = loBridge.CreateComValue()
loDecimal.SetDecimal(5.22)

loString = loBridge.CreateComValue("Hello World")

? "Original:"
? loInt.Value, loDecimal.Value

*** Call the method and pass ComArray parameter
loBridge.InvokeMethod(loNet,"PassByReference",;
                      loInt,loString,loDecimal)

*** Look at the result values
? "Updated:"
? loInt.Value, loDecimal.Value, loString.Value
```

All three of these values will update from the .NET method code.

Notice the use of the ComValue class to assign a value and then also retrieving the value back after the call is complete.

> If ComValue is used for a parameter wwDotnetBridge always updates the ComValue.Value property with the updated value after the call is complete **even if the method's parameter signature is not `ref` or `out`).

### Passing Arrays by Reference
To pass arrays by reference works the same way as above although it's not quite as obvious. You still need to assign the array to a ComValue structure, but most importantly you need to apply the array explictly to the value structure: 

```foxpro
loStrings = loBridge.CreateArray("System.String")
loStrings.AddItem("It's")
loStrings.AddItem("BigDay")

LOCAL loValue as Westwind.WebConnection.ComValue
loValue = loBridge.CreateComValue()
loBridge.SetProperty(loValue,"Value",loStrings)

lobridge.InvokeMethod(loNet,"PassArrayByReference",loValue)

loStringsResult =  loBridge.GetProperty(loValue,"Value")
? loStringsResult.Count
```