**wwBusinessObject** provides a base business object for CRUD (Created, Read, Update, Delete) services. This class is a single level business object class that implements both the business and data tier in a single object to provide ease of use and maximum performance. This class is a thin wrapper over the underlying data access layer using either direct FoxPro DAL commands or SQL Pass through for SQL backends.

It provides the following features:

## Easy CRUD - Automated Base Data Access
The base object provides core CRUD functionality through its base class methods. Native query based data access is also supported through native FoxPro or SQL syntax, using the class methods which can route to the appropriate server backend.

```foxpro
loCustBus = CREATEOBJECT("cCustomer")
loCustBus.New() 
? loCustBus.oData.LastName
loCustBus.oData.LastName = "Strahl Jr."
? loCustBus.Save()

*** save the new PK
pnPk = loCustBus.oData.Pk
? pnPk

* New instance to demonstrate
loCustBus = CREATEOBJECT("cCustomer")

? loCustBus.Load(pnPk)   && reload the record
? loCustBus.oData.LastName
loCustBus.oData.LastName = "Strahl"
loCustBus.Save()

loCustBus.Query("where pk = ?pnPk")  && load data
BROWSE

loCustBus.Delete(pnPk)
```

> #### @icon-warning Primary Keys Required for CRUD Operations
> Note: Automatic CRUD operations are based on single Primary keys. By default numeric keys are used but you can override the `CreateNewId()` method to create keys as strings or Guid values.

## FoxPro and SQL Backend Data Access  
Fox or SQL Server are natively supported. SQL Server is supported with SQL Pass through commands.The object also supports remote data access over the Web against a SQL Server backend (with some limitations a Fox backend can be used as well). It accomplishes this through CRUD helper methods that can handle many load/save/list operations without SQL code, and string based SQL commands for SQL query execution that can run on multiple data platforms.

The commands in the previous section all would work against SQL Server for example.

## **Internal Data Record Representation in .oData**  
Single record CRUD operation operate with an internal `.oData` member that receives database record data during `Load()` or `New()` operations.

The `.oData` member hold record data (record level operations) as a class and is completely disconnected from the database while loaded in memory - free to make changes as needed. `Save()` can then be used to write the changes into the database. 

## Query Handling via String based SQL Commands
Queries are run a string based SQL commands that are executed and returned as cursors by default. Errors are captured and return an error code and you can easily query the result count. There also options to return query results directly as JSON, XML, HTML and a few other formats.

Queries typically are run as strings using the `Execute()` method, but if you implement custom business objects with your own business class methods you can also use raw FoxPro commands to return result data. 

## Support for Validation
`wwBusinessObject` also includes a `Validate()` method and an `OnValidate()` handler method that business objects can implement to consistently validate business rules for field data and business rule validations.

## Web Focused but works for Desktop as well
This object framework is uniquely geared towards offline operation which is ideal for Web operations and passing data around the Web. In Web applications the object's `.oData` member is useful for holding Model data for databinding into HTML templates (ie. `<%= loCustomer.oData.FirstName %>` or binding to these values) and reading data back in bulk using [Request.FormVarsToObject()](VFPS://Topic/_02Q0ZGB86). 

The disconnected data model also makes it uniquely qualified for object aggregation by combining multiple objects into an object hierarchy. For example, an `oInvoice` object can contain `oInvoice.oCustomer` and `oInvoice.oLineItems` which are accessible through the single `oInvoice` object reference. Because of the self-contained nature of objects, it's easy to persist data to and from XML and pass them around the Web as well as making it very easy to use the business objects in HTML templates where data is bound to the object properties (`<%= oInvoice.oCustomer.oData.LastName %>`). Web Connection's object centric approach also allows you to pull in data back into the object from forms using `Request.FormVarsToObject()` resulting in very little code written to transfer data to and from HTML pages.

## Easy to get started with
Because the framework consists only of a minimal set of methods on this object it's easy to get started with it. Because of the single level implementation the framework is also very efficient. In addition a Wizard is provided to help you create business objects by mapping to tables.

**Required Dependencies**
* wwUtils.prg
* wwApi.prg
* wwCollections.prg

**Optional Dependencies**  
* wwSQL.prg (only if you use nDataMode = 2)
* wwHttpSql.prg (only if you use nDataMode = 4)
* wwXmlState.prg (only if you use Get/SetProperty methods)