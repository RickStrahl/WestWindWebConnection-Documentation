wwDotNetBridge provides an easy way to open up most of .NET's functionality to Visual FoxPro applications. While it relies on COM Interop to handle the interactions between FoxPro and .NET, wwDotnetBridge uses its own runtime hosting and .NET assembly to manage the .NET runtime in FoxPro.

This provides a number of functionality improvements over using COM Interop (ie. CreateObject("myApp.MyDotNetObject")) by greatly extending the range of what .NET features you can access with Visual FoxPro. Native COM Interop is severely limited by components that are exposed to COM explicitly in .NET [ComVisible(true)] and to components that can be instantiated from Visual FoxPro via CreateObject() (explicitly exported to COM). wwDotnetBridge - because it operates inside of .NET - can circumvent most of these limitations and provide access to a much wider range of features in .NET. For example, using wwDotNetBridge you can access static classes and their members, enum types, value types, generic types, Collections, interface based values as well as providing easier access and manipulation of arrays and collections from within FoxPro.

Another big feature is that .NET components do not need to be registered for COM - wwDotnetBridge can load components directly from within .NET so no COM registration is required for components loaded. This means if you create your own .NET components for use in your applications, these components don't need to be registered. It also means that you can access just about any .NET type directly from FoxPro, whether the type is registered for COM, marked as [ComVisible] or not. This opens up most of the .NET runtime to you for use in your own applications.

### How it works
wwDotnetBridge consists of three pieces:

* **wwDotnetBridge.prg**  
A FoxPro class that your application uses to access extended .NET features.

* **wwIPStuff.dll**  
Contains a small shim that loads the .NET Runtime into Visual FoxPro. Supports .NET 2.0 or 4.0 runtime loading explicitly. This feature is optional - you can also choose to load wwDotnetBridge using standard COM Interop Registry loading by using o = CREATE("wwDotNetBridge","V2",.T) which bypasses the custom loaded .NET runtime and use of wwIPstuff.dll. Note when using the latter approach wwDotnetBridge must be .NET COM registered with the RegAsm tool.

* **wwDotnetBridge.dll**  
A .NET component that acts as a proxy inside of .NET to perform extended tasks on the behalf of your FoxPro code. The FoxPro wwDotnetBridge class interacts with this .NET component to provide interaction with .NET. 

![](/images/wwDotNetBridge/wwDotnetBridgeArchitecture.png)


### A short Example
To see how wwDotnetBridge works it's easiest to show a simple example. The following example uses a third party `MarkDig` library to render Markdown text into HTML.

```foxpro
*** Load wwDotnetBridge 
do wwDotNetBridge

LOCAL loBridge as wwDotNetBridge
loBridge = GetwwDotnetBridge()   &&  CreateObject("wwDotNetBridge")


*** Load a third party library - full path or in FoxPro path
loBridge.LoadAssembly("markdig.dll")

lcMarkdown = "Hello **cruel world**."   && some markdown

*** Create an object instance - pipeline parameter for ToHtml
LOCAL loBuilder, loPipeline
loBuilder = loBridge.CreateInstance("Markdig.MarkdownPipelineBuilder")
loPipeline = loBuilder.Build()

*** Invoke a static method
lcHtml =  loBridge.InvokeStaticMethod("Markdig.Markdown","ToHtml",lcMarkdown,loPipeline, null)

? lcHtml
```

The first thing is to create an instance of wwDotnetBridge by either using `CreateObject("wwDotnetBridge")` or better yet use a cached version via `GetwwDotnetBridge()`. This creates the .NET Runtime instance and provides an instance of the .NET proxy to the instantiated FoxPro class.

Next you can call `LoadAssembly()` to load any .NET libraries that your code might need to reference. Note that most core .NET base library functions are available without any special load requirements, so typically you only load third party libraries as we have to with this Open Source `MarkDig.dll` library.

Once set up we can then create .NET types with:

```foxpro
loBuilder = loBridge.CreateInstance("Markdig.MarkdownPipelineBuilder")
```

You pass the **fully qualified .NET Classname** which is `namespace.classname`. `Markdig` is the namespace, `MarkdownPipelineBuilder` (anything after the last `.`) is the classname. Assuming the assembly that this class lives in is loaded, `CreateInstance()` then loads an instance of this class.

Note that the class you instantiate doesn't have to be **registered** any special way - wwDotnetBridge can instantiate the vast majority of .NET types directly in this manner. There are a few exceptions that can't be created:

* Generic Types (other `List<T>`, `Dictionary<T>` via `ComArray`)
* Delegates
* Expression Trees
* Any function as code

> There are workarounds for this by creating your own .NET components that **can** create instances of these objects and pass them back to FoxPro if needed or act on behalf of FoxPro inside of .NET.

`CreateInstance` instantiates any type you can reference in .NET passes back the instance to your FoxPro code. At this point you can use regular COM to access any COM capable functionality on the class - call methods and access properties. 

This works fine as long as the method or properties support types that work over COM. Unfortunately there are many things **that do not natively work over COM**. 

Among them are:

* Value types
* Generic types
* Virtual (inherited) members
* Arrays, Dictionaries, Collections, Lists etc.
* Enums
* Guids
* long
* Static methods and properties

and many more. 

In the code above we need to call a `static` methods, which is possible with wwDotnetBridge:

```foxpro
*** Invoke a static method
lcHtml =  loBridge.InvokeStaticMethod("Markdig.Markdown","ToHtml",lcMarkdown,loPipeline, null)
````

You can think of static methods and properties as **global methods** similar to a FoxPro UDF() - they don't belong to an instance of a class, and they do not have any state associated with them. Static methods typically are fully self contained and encapsulate all the work they do in themselves or other static methods in the same class. Static properties are used to often store global application state that can persist for the lifetime of the host application.

Anyway - the point is that you can't natively call a static method but `InvokeStaticMethod()` makes it possible. You pass the fully qualified name of a class and a method name plus any parameters to invoke the method.

### Direct and Indirect Object access
The above example is very simple and perhaps not so obvious of the feature set. Let's look at another example of something that requires a little more of wwDotnetBridges functionality.

The following receives a list of certificates installed on the local User Certificate store.

```foxpro
*** Load library
DO wwDotNetBridge

*** Create instance of wwDotnetBridge
LOCAL loBridge as wwDotNetBridge
loBridge = CreateObject("wwDotNetBridge")

*** Create an instance of 509Store
loStore = loBridge.CreateInstance("System.Security.Cryptography.X509Certificates.X509Store")

*** Grab a static Enum value
leReadOnly = loBridge.GetEnumvalue("System.Security.Cryptography.X509Certificates.OpenFlags","ReadOnly")

*** Use the enum value
loStore.Open(leReadOnly)

*** Collection of Certificates - X509CertificateCollection (custom)
laCertificates = loStore.Certificates

*** Collections don't work over regular COM Interop
*** so use indirect access
lnCount = loBridge.GetProperty(laCertificates,"Count")

*** Loop through Certificates - .NET uses 0 based collections/arrays
FOR lnX = 0 TO lnCount -1
	*** Access collection item indirectly because - custom collection 
	loCertificate = loBridge.GetProperty(loStore,"Certificates[" + TRANSFORM(lnX) + "]")
	
	IF !ISNULL(loCertificate)
		? loCertificate.FriendlyName
		? loCertificate.SerialNumber
		? loBridge.GetProperty(loCertificate,"IssuerName.Name")
		? loCertificate.NotAfter
	ENDIF
ENDFOR
```


Notice the GetEnumValue() function which retrieves an enum value. There are also calls to GetProperty() to retrieve the collection count (inaccessible through straight COM because it has a complex accessor function), and GetPropertyEx() to retrieve a collection item and a value on a child object. All of these would fail with straight COM interop calls.

There's much more to wwDotnetBridge but this should give you a basic idea of the functionality that this tool provides.