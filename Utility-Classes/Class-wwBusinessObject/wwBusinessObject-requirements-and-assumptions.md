The `wwBusinesObject` class makes several assumptions about the data that it is dealing with:

### Requires an Integer Primary or Character Key field
In order to avoid type conversions primary keys for this framework must be of type integer or string. Any integer or numeric field or string can be used or you can have one added to the table as needed by the Wizard.

### Requires an XML field for using GetProperty and SetProperty
`wwBusinessObject` includes a powerful dynamic property storage mechanism using a special XML field in the table. This mechanism allows you to store values of any kind without having to add additional fields. This is useful for infrequently used data or user definable data that extends the base data of an application. The XML field is not required if you don't use Set or GetProperty

### Single level business object
This business object framework is based on a simple business object base class called `wwBusinessObject`. This single level interface implements both the business logic and data access tier. This has been done mostly for performance as well as ease of use reasons to avoid excessive object creation requirements. As it stands the business object can run without subclassing at all, although it's recommended that each business object map to a table (or possible related structure) is abstracted into a separate subclass.

Different data sources are handled with bracketed code using the nDataMode property. For the most part the framework handles this abstraction through the base class methods, so your code will only require minmal bracketing for customized SQL statements that must run on multiple backends with different syntax. Currently VFP and SQL Server are supported although the base support should work with any SQL compliant backend.

### The oData member
The base business object is based on a table format since this is the most common scenario, but it can be customized as needed. Generation of the oData member can be overridden by subclassing the Load(), Find() and GetBlankRecord() methods to generate different object signatures. For example, you can run a custom SQL statement that pulls data from multiple tables and SCATTER NAME on that. Or you could create a custom object and use that as your oData member instead.

The base object has an oData member that will be used to hold the actual data of the object. The default load and find methods will load this member with the contents of the record requested. The data can then be modified via this oData member and its child properties. All operations at that point flow through the business object.

### Item lists can use the wwBusinessChildCollectionList object
The base business object does not handle multiline items. For this it uses a separate wwBusinessChildCollectionList object which includes a `LoadFromCursor()` method that loads an `oRows` collection member with SCATTER NAME records from each of the records in the specified cursor. The individual items can then be loaded into the appropriate objects as needed for full object manipulation.

As an alternative you can use object composition to create child item lists by adding collection properties to your top level entity `oData` objects to provide the child list functionality and manually load and save using the wwUtils [CursorToCollection()](VFPS://Topic/_3NL0UV9F6) and [CollectionToCursor()](VFPS://Topic/_45Y0X32PH) functions.