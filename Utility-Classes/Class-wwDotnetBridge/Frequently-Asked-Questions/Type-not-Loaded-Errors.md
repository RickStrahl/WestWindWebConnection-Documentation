This is an error that originates in .NET and is related to loading a type name that can't be found. 

If this occurs when you're trying to the load the service initially before making any method calls (ie. when you first create the FoxPro Proxy object with loService = CreateObject("myProxy","V2")) then this error is likely caused by the following issue:

[Calling a Service where the Class Name is different than the Service Name](vfps://Topic/_35S0NEXVD)

If this occurs when you're explicitly creating other types using loProxy.oBridge.CreateInstance() the most likely cause for this problem is that you didn't specify the full type name properly. Go into Reflector and pick out the class name 

![](/images/wwDotNetBridge/ReflectorClassName.png)