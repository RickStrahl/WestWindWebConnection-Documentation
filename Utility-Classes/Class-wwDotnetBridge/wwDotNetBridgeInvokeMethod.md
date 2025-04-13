Invokes a method on a .NET object instance.

While you can often call methods directly on an instance, the COM Interop mechanism has many limitations for calling .NET methods that have multiple overloads, have value, generic or incompatible types as parameters.

`InvokeMethod()` can be used when the method call will not work directly.

This method is limited to a maximum of 10 parameters. If you need more parameters use [InvokeMethod_ParameterArray()](vfps://Topic/wwDotNetBridge%3A%3AInvokeMethod_ParameterArray) instead.

Invoked method is fixed up so that arrays are passed/returned as ComArray instances, Guids as ComGuid.