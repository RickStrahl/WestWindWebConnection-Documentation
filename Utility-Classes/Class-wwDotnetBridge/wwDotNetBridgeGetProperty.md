Retrieves a property value from an object instance via Reflection. 

Use this method whenever you're dealing with an object such as a generic type of collection or struct that COM doesn't support direct access from FoxPro via COM. 

```foxpro
*** Simple Property Retrieval
lcValue = loBridge.GetProperty(loObject,"SomeProperty")

*** Nested Property Retrieval (use sparingly - better to retrieve object first)
lcValue = loBridge.GetProperty(loObject,"SomeObject.SomeProperty")

*** Indexers (int and string only)
lcValue = loBridge.GetProperty(loObject,"SomeCollection[0]")
lcValue = loBridge.GetProperty(loObject,'SomeCollection["name"]')

*** Indexers - on the current instance (int and string only)
lcValue = loBridge.GetProperty(loDataRow,"[0]")
lcValue = loBridge.GetProperty(loDataRow,'["name"]')
```