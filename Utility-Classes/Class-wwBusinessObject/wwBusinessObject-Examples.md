The following are a few simple examples of using `wwBusinessObject` based on the following class:

```foxpro
DEFINE CLASS cCustomer AS wwBusinessObject

cAlias = "Customers"
cFilename = "Customers"
cPkField = "Id"

************************************************************************
*  CreateNewId
****************************************
***  Function: Overridden from base to create Character key
***    Assume: If you don't override this method the assumed
***            type is integer and generating via lookup table
***    Return: new Character id
************************************************************************
FUNCTION CreateNewId()
RETURN SYS(2015)
ENDFUNC
*   CreateNewId

************************************************************************
*  Validate
****************************************
***  Function: Overrides Validation return .T. or .F. and set
***            .oValidationErrors collection via `.AddValidationError()`
***    Return: .T. or .F.
************************************************************************
FUNCTION Validate()

THIS.oValidationErrors.Clear()

IF EMPTY(THIS.oData.Company)
   THIS.AddValidationError("Company can't be empty","Company")
ENDIF

RETURN this.oValidationErrors.Count > 0
ENDFUNC
*   Validate

ENDDEFINE
```

Note you typically subclass `wwBusinessObject` and match it to a business entity that acts as a base of the business object. This doesn't have to be a one-to-one mapping - some business objects can represent complex objects like `Order` with subobjects for `Customer` and `LineItems`. The above is a simple business object mapped to a single object. 

### Queries
You can query data using the Query method, which lets you execute provider independent query. Assuming your queries work cross providers, a single query can be run against Fox data or Sql Server (or other ODBC backend) dependent on the `.nDataMode` property.

Queries can be partial using just a `WHERE` clause or `SELECT <fieldListOr*>` and the `FROM` or `SELECT` and `INTO` clauses are automatically filled in.

```foxpro
FUNCTION Query()

loBus = CREATEOBJECT("cCustomer")

*** Get all records
lnResult = loBus.Query()   && select * / TQUery
this.AssertTrue(lnResult > 0)
this.MessageOut(TRANSFORM(lnResult) + " records queried")

*** Get filtered records with augmented syntax (ie. no FROM clause)
lnResult = loBus.Query([Where company = "West Wind"],"TCustomers")   && Select *
this.AssertTrue(lnResult == 1)
this.MessageOut(TRANSFORM(lnResult) + " records queried for West Wind")

*** Explicit SQL
lnResult = loBus.Query([Select id,company Where company = "West Wind"],"TCustomers") 
this.AssertTrue(lnResult == 1)
this.MessageOut(TRANSFORM(lnResult) + " records queried for West Wind")
```

You should minimize using explicit query commands in your client code however and create specialized methods in the business object to return data. For example, in the `cCustomer`  class i may have a method called `GetRecentCustomers()` that I just pass a date to. The method would then create the appropriate SQL command to call with the Query method:

```foxpro
FUNCTION GetRecentCustomers(ldDate as DateTime) as Integer
RETURN THIS.Query( "SELECT * where Updated > ?ldDate", "TCustomers"  )
ENDFUNC
```

While this is a very simple example, even so it isolates the code in a single, logical location which makes this code easier to find and debug if something doesn't work or later needs to be updated.

### Load and Save  Entity Data
It's easy to load entity data, make changes and save it.

```foxpro
FUNCTION LoadAndSave()

lcCompany = "East Wind Trade Company"
loBus = CREATEOBJECT("cCustomer")

*** Load a specific record
this.AssertTrue(loBus.Load("_4FG12Y7UD"))

loBus.oData.Company = lcCompany
loBus.oData.Updated = DATETIME()

this.AssertTrue(loBus.Save())
ENDFUNC
```

### Add and Delete Entities
It's just as easy to create a new entity, fill it with data, then save it.

```foxpro
loBus = CREATEOBJECT("cCustomer")

*** Add a new Entity
loBus.New()
loBus.oData.Company = "East India Deliveries"
loBus.oData.FirstName = "Gaal"
loBus.oData.LastName = "Dornick"
loBus.oData.BillRate = 150
loBus.oData.Entered = DateTime()
this.AssertTrue(loBus.Save(),"Couldn't save new entity.")

*** Capture the Pk for new entity
lcId = loBus.oData.Id

*** Remove the newly created entity
this.AssertTrue(loBus.Delete(lcId))
```