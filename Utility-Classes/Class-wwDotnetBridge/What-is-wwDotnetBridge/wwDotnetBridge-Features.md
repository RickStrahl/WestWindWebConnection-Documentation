Here's a list of some of the more important features of wwDotnetBridge

### No Registration Required for .NET Assemblies
You can access most .NET assemblies using wwDotNetBridge, and these components do not need to be registered for COM in order to be used. If you create your own .NET components and want to interact with them from FoxPro you don't need to register these controls on your client's machines.

### Support for .NET 2.0 and 4.0 with a 2.0 assembly
wwDotnetBridge is built on .NET 2.0 so it works on systems running only .NET 2.0 (or 3.x). If .NET 4.0 is installed however it can work with .NET 4.0 assemblies as well. You can specify the version to use when an instance of the class is created via `CREATEOBJECT("wwDotnetBridge","V4")` or [GetwwDotnetBridge()](VFPS://Topic/_25E19D3DV).

### Access MOST .NET Components from FoxPro
wwDotnetBridge provides a .NET DLL that acts as a proxy to your FoxPro application. It runs inside of .NET and so has access to everything that .NET can do natively including instantiating any type, accessing static properties and values, enums, generics etc. When creating .NET components wwDotnetBridge passes back .NET objects via COM and you can interact with them either directly, just like with COM Interop when base features are supported, or indirectly through the Proxy for features not available through COM interop.

### Access Static Members
You can access static class members (methods and properties) using [GetStaticProperty()](VFPS://Topic/_24N1CFW5W), [SetStaticProperty()](VFPS://Topic/_24N1CFW6C), [InvokeStaticMethod()](VFPS://Topic/_24N1CFW5G). This allows access to many useful system components and also to a number of .NET types that have static factory methods.

### Create .NET Type Instances with Parameterized Constructors
Plain COM Interop can only instantiate .NET types that have parameterless constructors. Using the [CreateInstance()](VFPS://Topic/_24N1CFW52) method on wwDotnetBridge you can specify parameters when the type is created so you can create types that don't have parameterless constructors (of which there are quite a few in .NET).

### Access to Enumerated Values
You can access enumerations in .NET using [GetEnumValue()](VFPS://Topic/_24N1CFW5X) or [GetEnumString()](VFPS://Topic/_2A11C11XT) which retrieves enumerated values. This is important for any methods or properties that except enumerated type arguments.

### Access to Value Types
Using COM Interop alone value types are not accessible over COM. They cannot be passed back to FoxPro. Using indirect access with [GetProperty()](VFPS://Topic/_24O056G96), [SetProperty()](VFPS://Topic/_24O0585YS) or [InvokeMethod()](VFPS://Topic/_24O04PA5V) you can access value types directly.

### Support for accessing Generic .NET Types
COM Interop cannot access generic types directly. Using [GetProperty()](VFPS://Topic/_24O056G96), [SetProperty()](VFPS://Topic/_24O0585YS) and [InvokeMethod()](VFPS://Topic/_24O04PA5V) you can access Generic types indirectly set and return values from them.

### Access overloaded Methods by their original Names
.NET supports method overloading - 1 method name with several different implementations and parameter signatures. Using plain COM Interop these overloaded methods are difficult to figure out as they are remapped to Method(), Method_2(), Method_3() and so on. Using InvokeMethod() you can call overloaded methods using their standard names and parameters.

### Event Handling without Interfaces
You can use wwDotnetBridge to handle events from .NET objects by mapping them to FoxPro objects that match the event callback signature. There's no requirement to implement an interface or implement all of the interface methods. Use [SubscribeToEvents()](VFPS://Topic/_57Q019PW0) to create an event subscriptions, and pass a handler object with methods to match event signatures.

### Many Helpers for Arrays, Lists and Collections
Arrays over COM Interop are finicky at best. Updating arrays from FoxPro is very difficult and in some situations impossible using plain COM Interop. When using indirect access with GetProperty(),SetProperty() and InvokeMethod() you can use ComArray instances to easily manipulate arrays and lists in .NET and access them from FoxPro without ever passing the array into FoxPro. ComArray conversion happens automatically when arrays are set or passed as parameters or returned as results from methods. Likewise any array results from InvokeMethod() or GetProperty() are returned as ComArray instances that can be manipulated and updated using this proxy instance.

Additionally there are many other helper methods that allow you to manipulate arrays from within FoxPro code without the arrays being copied to FoxPro. Methods like CreateArrayInstance(), AddArrayItem(), GetArrayItem(), RemoveArrayItem() manipulate the array in .NET and so maintain integrety of the array with the abillity to create, add, update and remove/clear items from the array.

### Auto-Conversion Routines
If you're using InvokeMethod(), GetProperty() and SetProperty() many problem types are automatically fixed up. If you use arrays they are automatically converted into ComArrays. Guids are turned into ComGuids. Binary values are automatically marshalled into proper .NET byte arrays. DataSets returned are automtically turned into XmlAdapters and we provide additional conversion routines to turn the XmlAdapter into a cursor or all cursors. Null values are automatically converted to and from COM DbNull objects.

### Type Conversion Routines with ComValue
Convert many problematic types into the appropriate .NET types. Byte, byte[], null, Guid, Int16, Int64, decimal etc. etc. all can be converted into their proper .NET types at runtime with a simple ConvertToDotNetValue() function or explicitly with the ComValue class.