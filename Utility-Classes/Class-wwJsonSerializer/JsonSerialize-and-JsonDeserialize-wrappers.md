There are two helper methods that provide single line of code JSON serialization and deserialization.

Note that these are standalone UDF() functions rather than class methods:

### JsonSerialize()
```foxpro
lcJson = JsonSerialize(loObjectOrValue, llFormat)
```

`llFormat` determines whether the JSON is indented.

### JsonDeserialize()

```foxpro
loObject = JsonSerialize(lcJson)
```

### Example
Here's an example of how to use these 
```foxpro
DO wwJsonSerializer

*** Create an object
loObject = CREATEOBJECT("Empty")
ADDPROPERTY(loobject, "Name", "Rick")
ADDPROPERTY(loObject, "Company", "West Wind")
ADDPROPERTY(loObject, "Entered", DATETIME())

*** Serialize to JSON
lcJson = JsonSerialize(loObject, .t.)
? lcJson

*** Deserialize from JSON into an Object
loObject = JsonDeserialize(lcJson)
? loObject.Name
? loObject.Company
? loObject.Entered
```