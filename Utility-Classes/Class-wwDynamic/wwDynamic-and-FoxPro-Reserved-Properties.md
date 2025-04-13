`wwDynamic` is implemented as a Relation class which has an internal `__Reference` member that holds the actual dynamic field values. The reason for the base class is that `EMPTY()` unfortunately does not allow for any method hooks so it can't be automated. As a result a FoxPro base class has to be used to provide a smooth integration that allows child objects to be created without having to declare them explicitly:

```foxpro
loCust = CREATEOBJECT("wwDynamic")
loCust.firstName = "Rick"
loCust.lastName = "Strahl"

*** You can simply assign new values and wwDynamic
*** creates the object chain on the fly
loCust.address.Street = "321 Fright Lane"
loCust.address.City = "Paia"
```

This is possible because the object can intercept the property access and route it to an internal reference.

### Reserved FoxPro Properties
The problem with this approach however is that FoxPro has a few reserved property names - basically the base class properties used on `RELATION` in this case which means you can't explicitly access these internal properties using FoxPro language. You can use the `GetProp()` method however.