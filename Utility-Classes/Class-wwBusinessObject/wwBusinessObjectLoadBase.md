A low level version of the Load() method that can be customized with a custom filter expression and a custom field list to load individual records into the oData member. 

This method is useful to create custom overridden Load() or Load() derivates (LoadByUserName() for example) methods for business objects that retrieve data in other ways than by specification of a primary key.

To create a customized  Load() derivative method in your business object you could do:

```foxpro
FUNCTION LoadByUserName
PARAMETERS pcUsername
RETURN this.LoadBase("username=?pcUserName")
```

Then in code:

```foxpro
loUser = CreateObject("busUser")
IF !loUser.LoadByUserName("rstrahl")
   RETURN 
ENDIF
? loUser.oData.Username
```

You can also use LoadBase() directly for custom scenarios where you would like to retrieve entities with custom field lists for example, when returning results back to a client application via serialized AJAX JSON results where less data on the wire is preferrable.