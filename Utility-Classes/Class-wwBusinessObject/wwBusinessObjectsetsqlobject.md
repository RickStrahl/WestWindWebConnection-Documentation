Assigns existing SQL object to the oSQL object or creates one if a connection string is passed. Handles updating data mode and other admin tasks.

You can directly open a connection with your business object like this:

```foxpro
* ** Simple syntax example
loInv = CREATEOBJECT("cInvoice")
loInv.SetSQLObject("DSN=Pubs;uid=sa;pwd=;")
```

Or you can explicitly create a wwSQL object and pass it to the business object:
```foxpro
loSQL = CREATEOBJECT("wwSQL")
loSQL.Connect("DSN=Pubs;uid=sa;pwd=;")

loInv = CREATEOBJECT("cInvoice")
loInv.SetSQLObject(loSQL)

loCust = CREATEOBJECT("cCustomer")
loCust.SetSQLObject(loSQL)
```

This latter approach will be better as it will allow you to share a single wwSQL object for all of your business objects, where the former approach would create multiple wwSQL objects for each business object.

If you have nested objects and you need to pass connections forward to nested objects use code like the following:

```foxpro
FUNCTION oInvoice::Load

oCustomer = CREATE("cCustomer")
oCustomer.SetSQLObject( THIS.oSQL )
...
```