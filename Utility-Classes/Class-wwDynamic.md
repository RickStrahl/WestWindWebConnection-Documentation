This class allows you to create or extend an object with 'dynamic' properties by simply referencing non-existing properties on the instance. Any non-existing properties you reference will automatically be created - this model is similar to the way JavaScript or Expando objects work.

In essence this is a cleaner and non-declarative way to write `ADDPROPERTY()` logic. This makes a great tool for creating complex objects on the fly in user code which is especially useful when creating data transfer objects for JSON or XML Services to transmit.

Here's an example of what you can do:

```foxpro
*** Extend an existing object
loCust = CREATEOBJECT("cCustomer")
loItem = CREATEOBJECT("wwDynamic",loCust)

*** Alternately you create a new object (EMPTY class) 
* loItem = CREATEOBJECT("wwDynamic")

*** Add new properties that don't exist on cCustomer
loItem.Bogus = "This is bogus"
loItem.Entered = DATETIME()

? loItem.Bogus
? loItem.Entered

*** You can even create nested objects as a chain 
loItem.oChild.Bogus = "Child Bogus"
loItem.oChild.Entered = DATETIME()

? loItem.oChild.Bogus
? loItem.oChild.Entered

*** Access original cCustomer props and methods
? loItem.cFirstName
? loItem.cLastName
? loItem.GetFullName()
```

### Help with JSON Serialization and Property Casing
For JSON serialization you can also use the explicit `.AddProp()` method which automatically sets an internal `__PropertyNameOverrides` list for non lower-cased property names. By default properties are JSON serialized as lower case, and this override list allows for proper casing on property names.

```foxpro
loMessage = CREATEOBJECT("wwDynamic")
loMessage.sid = ""   && prop is non-cased (lower)
loMessage.AddProp("dateCreated", DATETIME())
loMessage.AddProp("dateUpdated", DATETIME())
loMessage.AddProp("dateSent",DATETIME())
loMessage.AddProp("accountSid","")
loMessage.AddProp("apiVersion","")

*** Empty Value creates wwDynamic instance
loMessage.AddProp("fileInfo")
loMessage.FileInfo.AddProp("fileTime", DateTime())
loMessage.FileInfo.attribute = "A"  && prop is non-cased (lower)

* "dateCreated,dateUpdated,dateSent,accountSid,apiVersion,fileInfo,fileTime"
? loMessage.__PropertyNameOverrides 

? loMessage.DateCreated
? loMessage.DateUpdated

loSer = CREATEOBJECT("wwJsonSerializer")
? loSer.Serialize(loMessage, .T.) && produces properly cased property names
```

which produces the following serialized JSON:

```foxpro
{
  "accountSid": "",
  "apiVersion": "",
  "dateCreated": "2020-01-02T21:23:14Z",
  "dateSent": "2020-01-02T21:23:14Z",
  "dateUpdated": "2020-01-02T21:23:14Z",
  "fileInfo": {
    "attribute": "A",
    "fileTime": "2020-01-02T21:23:14Z"
  },
  "sid": ""
}
```