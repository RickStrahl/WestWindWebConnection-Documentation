A global wwNameValueCollection object that can be used to store 'static' or 'global' values and properties to avoid extending the wwServer instance with new properties. 

It's a good idea to avoid adding many properties to your wwServer subclass as it adds to the COM interface and every time properties are added or removed the server needs to be re-registered. To avoid this rather than attaching properties to your wwServer instance attach them to `.oResources`.

oResources allows two types of expansion:

* Via .AddProperty() as a custom property
* Via .Add(lcItem,lcValue) as a wwNameValueCollection entry
Both types are persistent through the lifetime of the application and stay in memory for that duration and always accessible via Server.oResources in Process code.

### Creating Global Properties
To create new 'properties' on the oResources object use code like the following:

```foxpro
* MyAppServer :: OnLoad()
PROTECTED FUNCTION OnLoad()

this.oResources.AddProperty("oSql",CREATEOBJECT("wwSql"))

IF !this.oResources.oSql.Connect("database=WestWindAdmin")
   throw this.oResources.oSql.cErrorMsg
ENDIF

ENDFUNC
```

### Creating Global Collection Entries
If you need values that need to be looked up via keys it's better to use collection values.

```foxpro
* MyAppServer :: OnLoad()
PROTECTED FUNCTION OnLoad()
this.oResources.Add("AppName","West Wind Sample Application")
ENDFUNC
```

Typically you will define global variables like this during startup of the application for which the wwServer's OnLoad method is best. But realistically you can add resources at any time during the application's execution.

To use the value you can simple use code like this (here in a Process method):

```foxpro
FUNCTION MyProcessFunction()
Response.Write( Server.oResources.Item("AppName") )
ENDFUNC
``` 

or in a template expression:

```html
<%= Server.oResources.Item("AppName") %>
```