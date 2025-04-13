This method explicitly adds a new property to this `wwDynamic` instance **and** it automatically adds the property to the `__PropertyNameOverrides` list which is used during JSON serialization to produce properly cased serialization output.

The primary use case for this method is:

* If you need to preserve property name case for JSON Serialization
* If you need better performance for adding properties
* Set properties with internal FoxPro base class names:  
`Name`, `Class`, `Tag`, `Parent` etc.

Here are some examples that demonstrate:

```foxpro
loMessage = CREATEOBJECT("wwDynamic")

*** Create a name property
loMessage.name = "Rick"

*** same as (no benefit since name is already lower case always)
loMessage.AddProp("name","Rick")

*** This has benefit of supporting mixed cases via __PropertyNameOverrides
loMessage.AddProp("lastName","Strahl")

*** This loses mixed case for property name serialization
loMessage.lastName = "Strahl"
```

> This method has a funky name of `AddProp()` as it cannot have the same name as `AddProperty()` which is a base class method that can't be completely overridden. Hence the alternate name to avoid the base class firing and failing on creation of FoxPro reserved properties.