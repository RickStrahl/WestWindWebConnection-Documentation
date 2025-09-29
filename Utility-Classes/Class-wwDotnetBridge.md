<small>

**Shortcuts:**  

* [Core Class Members List](#classmembers)
* [Support Functions List](VFPS://Topic/_7030OQLEK)

</small>

wwDotnetBridge allows you to access the vast majority of .NET components directly from FoxPro. It provides registrationless activation of .NET Components, and acts as a proxy into .NET that makes it possible to access features that native COM Interop does not support directly.

To work around COM limitations, wwDotnetBridge provides many improvements and work arounds, while still using the base layer of COM Interop for the inter-process communication. Everything that works with native COM Interop also works with wwDotnetBridge - it's the same technology after all -  but you get many more support features and automatic type translations to work around the limitations.

The key features of wwDotnetBridge are:

* **Registrationless access to most .NET Components**  
Unlike native COM Interop, you can instantiate and access .NET Components and static classes, without requiring those classes to be registered as COM objects. Objects are instantiated from within .NET, so you can access most .NET components by directly loading them from their DLL assembly. Both .NET Framework (`wwDotnetBridge`) and .NET Core (`wwDotnetCoreBridge`) are supported.

* **Instantiates and Interacts with .NET Objects via COM from within .NET**  
wwDotnetBridge is a .NET based component that **runs inside of .NET** and acts as a proxy for activation, invocation and access operations. It creates **any** .NET instances from within .NET and returns those references using COM Interop. Once loaded you can use all features that COM supports directly: Property access and method calls etc. *as long the members accessed use types that are supported by COM*.

* **Support for Advanced .NET Features that COM Interop doesn't support**  
Unfortunately there are many .NET features that COM and FoxPro don't natively support directly: Anything related to .NET Generics, overloaded methods, value types, enums, various number types to name just a few. But because wwDotnetBridge runs inside of .NET, it can proxy invocations via Reflection and access most features regardless of whether they are supported over COM or FoxPro. The core helper methods are `InvokeMethod()`, `GetProperty()` and `SetProperty()` as well as their static counterparts.

* **Automatic Type Conversions and Type Helpers**  
Because there are many incompatible types in .NET that don't have equivalents in COM or FoxPro, wwDotnetBridge performs many automatic type conversions when using the above proxy methods. This make it easier to call methods or retrieve values from .NET by automatically converting to FoxPro compatible types. For example: decimals to double, long, byte to int, Guid to string etc. Some types can't be passed into FoxPro at all, so there are also wrapper classes like `ComArray` that wraps .NET Arrays and Collections and provides a FoxPro friendly interface for navigating and updating collections, and `ComValue` which wraps incompatible .NET values and provides convenience methods to set and retrieve the value in a FoxPro friendly way and pass it to .NET methods or property assignments.

* **Support for Async Code Execution**  
A lot of modern .NET Code uses async functionality via `Task` based interfaces, and wwDotnetBridge includes a `InvokeTaskMethodAsyc()` helper that lets you call these async methods and receive results via Callbacks asynchronously. You can also run **any** .NET synchronous method and call it asynchronously using `InvokeMethodAsync()` using the same Callback mechanism.

### Getting Started
The first step in using wwDotnetBridge is to load it for the first time, which instantiates the .NET Runtime. We recommend that you do this somewhere in your application startup sequence so as to avoid any potential version ambiguities. 
```foxpro
DO wwDotnetBridge               && Loads dependencies
InitializeDotnetVersion()   && Loads .NET Runtime and caches it
```

`InitializeDotnetVersion()` is *optional*. You can use `GetwwDotnetBridge()` or `CREATEOBJECT("wwDotnetBridge")` which do the same thing, but using `InitializeDotnetVersion()` explicitly describes the purpose which is to reliably and predictably load .NET **on startup**. Additionally it ensures if there's some problem with .NET runtime loading you know about it before the app starts.

> #### @icon-warning  Unable to load CLR Instance Errors
> If you get an  <b>Unable to CLR Instance</b> error when creating an instance of wwDotnetBridge, you probably need to unblock the wwdotnetbridge.dll or need to ensure that the wwdotnetbridge.dll and wwipstuff.dll are in your FoxPro path. Please see <%= TopicLink([Unable to load CLR Instance],[_3RF12JTMA]) %> for more info.

> #### @icon-info-circle Loading DLLs from Network Locations: Configuration required
> .NET components require explicit configuration in order to support remote loading from network locations. This is done by creating a configuration file for your application `yourapp.exe.config` or the VFP IDE `vfp9.exe.config`, in their respective startup folders. We recommend at minimum you use the following `.config` file settings:
> ```xml
> <?xml version="1.0"?>
> <configuration>
>   <runtime>
>       <loadFromRemoteSources enabled="true"/>
>   </runtime>
> </configuration>
> ```

### wwDotnetBridge Example
With the library loaded, you can retrieve an instance by calling the `GetwwDotnetBridge()` factory function which caches a loaded wwDotnetBridge instance and therefore is very fast to access.

Here's an example what of some of what you can then do:

```foxpro
*** Create or get cached instance of wwdotnetbridge
LOCAL loBridge as wwDotnetBridge
loBridge = GetwwDotnetBridge()

*** The first two are built-in .NET Framework functions so no assembly has to be loaded

*** Create a built-in .NET class and execute a method - this one downloads a file to disk
loHttp = loBridge.CreateInstance("System.Net.WebClient")
loHttp.DownloadFile("http://west-wind.com/files/MarkdownMonsterSetup.exe",
                    "MarkdownMonsterSetup.exe")

*** Format a string: Static method: Typename as string, method, parameters
? loBridge.InvokeStaticMethod("System.String","Format","Hello {0}. Time is: {1:t}",;
                              "Rick", DATETIME())
* Hello Rick. Time is: 2:45 PM                              

*** Now load a third party Assembly - assemblies load their own dependencies!
? loBridge.LoadAssembly("wwDotnetBridgeDemos.dll")

*** Create a class Instance - naming is: namespace.class
loPerson = loBridge.CreateInstance("wwDotnetBridgeDemos.Person")

*** Access simple Properties - plain COM
? loPerson.Name
? loPerson.Company
? loPerson.Entered

*** Call simple method - plain COM
? loPerson.ToString()
? loPerson.AddAddress("1 Main","Fairville","CA","12345")  && 2 Addresses now

*** Access an Array/Collection of Objects and iterate over the list
*** Arrays/Collections/Dictionaries are not easily accessible via COM
*** wwDotnetBridge returns a `ComArray` instance
loAddresses = loBridge.GetProperty(loPerson,"Addresses");

*** Access ComArray.Count list count
lnCount = loAddresses.Count   && 2 addresses

*** Access the first item - 0 based list
loAddress = loAddress.Item(0);
? loAddress.Street
? loAddress.ToString()

*** Add another item to the array
* loNewAddress = loBridge.CreateInstance("wwDotnetBridgeDemos.Address")
loNewAddress = loAddresses.CreateItem()
loNewAddress.Street = "122 Newfound Landing"
loNewAddress.City = "NewFoundLanding"
loAddresses.Add(loNewAddress)

? TRANSFORM(loAddresses.Count) + " Addresses"  && 3

*** Iterate through the entire list (3 items now): Remember 0 based!
FOR lnX = 0 to loAddresses.Count -1 
    loAddress = loAddresses.Item(lnX)
    ? loAddress.ToString()
    ? 
ENDFOR
```

All interactions occur over COM so any object instances are COM objects with typical .NET COM behavior (no Intellisense, COM style errors). Any properties and methods that use standard types can be **directly accessed** via their normal COM property and method names. Any members or methods that use types that are incompatible with COM (Value types, Generics, Long, Decimal, Guid etc.) have to use the indirect access methods  `GetProperty()`, `SetProperty()` or `InvokeMethod()` for access.

> If direct access fails for whatever reason, always try the indirect methods.

For much more detailed wwDotnetBridge and .NET Interop information you can also check out the white paper:

* <a href="https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=57032" target="top">wwDotnetBridge White Paper</a>.