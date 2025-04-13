To facilitate the process of creating specific subclasses for your business objects Web Connection provides the **Business Object Wizard** which connects a table to a business object. Note that although a table is the most common scenario, tables are not required for the business object.

To start the Wizard do:

```foxpro
DO Console WITH "BUSOBJECT"

*** For Client Tools
* DO wwclient_Console with "BUSOBJECT"
```

which brings up the Wizard form:

![](//images/misc/BusObjectBuilder.png)

**Base Class**  
You can specify a base class for the new class that is to be created. By default this will be `wwBusinessObject` in `wwBusinessObject.prg` and that's the recommended base class. You can also use `wwBusiness.vcs` and `wwBusiness` or choose any base class you subclassed yourself (an application base business object.)

**Name of the class**  
Pretty obvious - this will become the business object class name.

**Class library**  
Where to store the new business object. This can either be a PRG files for subclassing from `wwBusinessObject` or a VCX class when subclassing from `wwBusiness`.

**Base Table**  
The Web Connection business objects tend to link to tables - so pick a table that you want as your base table. You may not actually use the table later on but you need to specify one here. If you're using SQL Server data only just type in the name of the file, otherwise go ahead and pick the file.
For Fox tables I suggest that you use relative paths such as .\wwstore\wws_items.dbf instead of fully hardcoded paths. Relative paths port much easier if you move the application.
For Sql Server you should specify just the table name.

**ID Table**  
For Fox data only you can specify the name of the ID table. If the table doesn't exist one will be created. For SQL Server the table name always will be wws_id and it will be created if it doesn't exist. The Wizard will add a record to this table with the name of the base table.

**PK Field**  
Specify the field that will be used as the primary key field. Remember this field must be a numeric (Integer preferrably) field in order to be able to accept the PK generation features.  If you choose a different type you'll have to override the `NewId()` method to handle the ID generation.

For Fox data you can specify a field name here and the Wizard will add that field to the ID table. For SQL data you have to manually add the field to the table.

**Fox/SQL Data options**  
Choose whether you want to work with Fox or SQL Server data or both. This option controls the ID table updates.

**CreateTable() and Reindex() generation**  
If these options are checked these methods are automatically generated for you based on the structure of the table that the business object is bound to. Note, this will overwrite any code in those methods and generates code only for the Fox tables, not SQL Server tables (`nDataMode=0`). If you have custom code here you can use the[ Table and Index Structure Exporter](vfps://Topic/Table%20and%20Index%20Structure%20Exporter) utility and paste the generation code manually.

**SQL Connection**  
If you want to use SQL Server provide a connection string that will allow you to connect to the SQL database you want to connect to. This value will also become the default connection string used by the object (which you can of course override with code at any time). 

> Note that connection strings default to `server=.;integrated security=yes;` and you can optionally omit those values unless you want to use something different. To connect to a DB named `WebStore` on the local server with integrated security it's enough to specify: `database=webstore`.

**Generate Class**  
Determines whether a class is generated. You can turn this off if all you want to do is generate the ID table entry.

**Generate ID Table Entry**  
Determines whether the ID table is created and/or updated. The value of the counter is set to the highest PK + 1.

Note that you can run the Wizard multiple times without worrying about overwriting code. The Wizard only writes property values so existing class code is not affected. The exception to this are the `CreateTable()` and `Reindex()` options which will overwrite the respective method code with newly generated code.

A typical multiple use scenerio will be:
* Create object for fox data
* Come back to create SQL entries - run only the ID generation
* Come back to update the ID table entry
* Come back to update the CreateTable() and Reindex() methods when structure changes occur.

The Wizard remembers your last settings with a config stored in your temp directory.

### What's generated
The Wizard generates a business object class that is a subclass from `wwBusinessObject` (or whatever class you chose in the Wizard). The class is mostly empty except for the following generated methods (if you chose Fox data):

**Empty Overloaded Methods**

* `Load()` 
* `Save()` 
* `Validate()` 

These are there simply as placeholders and can be removed if you don't need them. But these methods are often overridden to provide custom load, save or validation before save behavior. 

**FoxPro File Administration Helpers**

* `CreateTable()`
* `Reindex()` 

These methods provide FoxPro file creation and reindexing functionality based on the table's original structure.

> **Important:** The structure is **not updated** when the tables are changed. You have to manually update these methods or regenerate them. You can use `tools\crt_dbf.prg` to generate new structure and reindex commands from a table or cursor.

Here's the actual class output generated by the Wizard:

```foxpro
DO wwBusinessObject
SET PROCEDURE TO ccustomer.prg ADDITIVE



*************************************************************
DEFINE CLASS cCustomer AS wwBusinessObject
*************************************************************

cAlias = "customers"
cDataPath = "c:\webconnection\fox\samples\wwdemo\"
cFileName = "customers"

cPkField = "pk"
cIdTable = "c:\webconnection\fox\samples\wwdemo\cust_id"

*** Sql Connection String
cConnectString = "database=test"

*** 0 - fox 2 - Sql 4-Web
nDataMode = 0

#IF .F.
LOCAL THIS as ccustomer of ccustomer.prg
#ENDIF


************************************************************************
*  Validate
****************************************
***  Function: Add custom validation Logic here or remove if you
***            have none. Call explicitly or with `lAutoValidate = .t.`
***    Return: .T. for no errors, .F. if you do
************************************************************************
FUNCTION Validate()

*!*	THIS.ValidationErrors.Clear()

*!* *** Put your custom validation logic here
*!*	IF LEN(this.oData.Author) < 6
*!*	   this.AddValidationerror("Author name has to be at least 6 characters")
*!*	ENDIF

*!*	IF THIS.ovalidationerrors.COUNT > 0
*!*	    THIS.SetError(THIS.ovalidationerrors.ToString())
*!*	    RETURN .F.
*!*	ENDIF

RETURN .T.
ENDFUNC
*   Validate


************************************************************************
*  Load
****************************************
***  Function: Use this to override default Load behavior for adding
***            things like child objects/collection, or updating loaded
***            values on load.
***    Assume: Make sure to call `DODEFAULT()`.
***      Pass:
***    Return:
************************************************************************
FUNCTION Load(lvId)
RETURN DODEFAULT(lvId)
ENDFUNC
*   Load

************************************************************************
*  Save
****************************************
***  Function: Use this optionally override save behavior. Often used
***            to update values like timestamps or fixup values before
***            saving. Make sure to call `DODEFAULT()`
***    Assume: Make sure to call `DODEFAULT()`.
***    Return: .T. or .F.
************************************************************************
FUNCTION Save()
RETURN DODEFAULT()
ENDFUNC
*   Save

*************************************************************
FUNCTION CreateTable(lcFileName)
*************************************************************
* ...
ENDFUNC
* CreateTable

*************************************************************
FUNCTION Reindex()
*************************************************************
* ...
ENDFUNC
* Reindex

ENDDEFINE
* ccustomer

```

The class is basically empty except the admin for the methods and the idea is that you add your own custom methods for custom queries and augmenting the CRUD operations like `Load()` and `Save()` or `OnValidate()`

### Fixing up File Location Paths
You may notice that the Wizard creates hard coded paths for your business object class. Generally **this is not a good idea** because hardcoded paths are not portable if the project gets moved:

```foxpro
cAlias = "customers"
cDataPath = "c:\webconnection\fox\samples\wwdemo\"
cFileName = "customers"

cPkField = "pk"
cIdTable = "c:\webconnection\fox\samples\wwdemo\cust_id"
```

It's highly recommended that you do one of two things:

* Use Application Relative Paths
* Make sure `SET PATH` includes the data paths

Using Relative paths looks like this:

```foxpro
cAlias = "customers"
cDataPath = ".\samples\wwdemo\"        && relative to app start path
cFileName = "customers"

cPkField = "pk"
cIdTable = ".\samples\wwdemo\cust_id"  && relative to app start
```

Using `SET PATH` looks like this:

Somewhere during application startup:

```foxpro
SET PATH TO '.\samples\wwdemo` ADDITIVE
```

Then in the business object:

```foxpro
cAlias = "customers"
cDataPath = ""          && no path - it's in SET PATH              
cFileName = "customers"

cPkField = "pk"
cIdTable = "cust_id"    && no Path
```

### Adding Your own Methods
A business object becomes a business object only once you start adding functionality:

* Query methods 
* Business Operations and Calculations
* Validations
* Custom Insertions/Data Manipulation

Here are some examples:

#### Query Methods
This typically takes the form of building up a query string from various input parameters and calling the `Query()` method:

```foxpro
PROCEDURE GetWeblogEntries
LPARAMETERS lnCount, lcFields, lcFilter, lcCursor

IF EMPTY(lcCursor)
    lcCursor = "TEntries"
ENDIF

IF !EMPTY(lnCount)
    lcSql = "SELECT TOP " + TRANSFORM(lnCount) + " "
ELSE
    lcSql = "SELECT "
ENDIF

IF EMPTY(lcFields)
    lcFields = "title,body,active,entered,bodymode,pk "
ENDIF

lcSql = lcSql + lcFields + " FROM " + ;
    THIS.cfilename

IF !EMPTY(lcFilter)
    lcSql = lcSql + " Where " + lcFilter
ENDIF

lcSql = lcSql + " ORDER BY ENTERED DESC "

RETURN THIS.QUERY(lcSql,"TEntries")
ENDPROC
```

Although the `Query()` or `Execute()` methods are recommended for command execution in order to provide some abstraction and the possibility of switching to a SQL backend, you can also just run raw FoxPro query commands here - as long as you return the `_TALLY` or `Reccount()` from query results for consistency.

THe code above could just do:

```foxpro
SELECT title,body,active,entered,bodymode,pk ;
    FROM blog_entries ;
    WHERE entered > DATE() - lnDays
    INTO CURSOR TEntries
    
RETURN _TALLY    
```

The main goal is not about the mechanics, but abstracting your business logic including queries into reusable methods that are:

* Easy to find
* Easy to debug
* Easy to fix in one place if used often

#### Calculation or Helper Functions related to Business Rules 
Another common task is to calculate some values related to a business entity. The following calculates the Weblog stats displayed on the home page of the Web Connection Weblog and returns an object. As such this is a custom function that returns some specific data related to Weblog entries:

```foxpro
FUNCTION CalculateWeblogStats
LOCAL loStats, lnResult

loStats = CREATEOBJECT("EMPTY")

ADDPROPERTY(loStats,"Posts",0)
ADDPROPERTY(loStats,"Comments",0)
ADDPROPERTY(loStats,"Trackbacks",0)

lnResult = THIS.QUERY("select COUNT(pk) as Posts from " + THIS.calias + " where active","TStats")
IF lnResult > 0
    loStats.Posts = TStats.Posts
ENDIF

lnResult = THIS.QUERY("select COUNT(pk) as Comments from blog_comments where active","TStats")
IF lnResult > 0
    loStats.Comments = TStats.Comments
ENDIF

RETURN loStats
ENDPROC
```

Note that helper functions can be very simple and still be useful and they don't necessarily have to access the database to live in the business object. For example this method simply returns formatting for a `GravatarLink()` for the active Weblog Comment:

```foxpro
PROCEDURE GravatarImagelink
LPARAMETERS lcEmail, lnSize
IF EMPTY(lnSize) 
   lnSize = 85
ENDIF
RETURN GravatarLink(lcEmail,lnSize)
ENDPROC
```

#### Updating Values on Save
Another common operation is an override for the `Save()` method to update values before they are written to the database. Here the Updated field is updated before writing:

```foxpro
PROCEDURE Save

IF EMPTY(THIS.oData.Abstract)
    THIS.oData.Abstract = TextAbstract(THIS.oData.Body,200)
ENDIF

THIS.oData.UPDATED = DATETIME()

return DODEFAULT()
ENDPROC
```

Note that you **have to call `DODEFAULT()` to ensure the base save behavior is firing.

If you've customized your `.oData` object on load to hold additional data than what's in the mapped table, you'll need to make sure you also write that data back to the database.

#### Validation
Validation is important for most business objects and you can override the `Validate()` method to provide custom validation logic that checks for certain conditions and then adds any validation errors into the `ValidationErrors` collection:

```foxpro
PROCEDURE Validate

THIS.oValidationErrors.Clear()

IF LEN(this.oData.Author)
   this.AddValidationerror("You have to specify a name","Author")
ENDIF

IF LEN(THIS.oData.Body) < 20
    THIS.Addvalidationerror("Body needs to be longer than 20 characters.","ody")
ENDIF
IF EMPTY(THIS.oData.Title)
    THIS.Addvalidationerror("The title cannot be left blank","Title")
ENDIF

*** If we have errors added - return .F.
IF THIS.oValidationErrors.Count > 0
    THIS.SetError(THIS.oValidationErrors.ToString())
    RETURN .F.
ENDIF

RETURN .T.
ENDPROC
```

The `Validate()` method can either be called explicitly via code, or you can set `loBus.lAutoValidate = .T.` to automatically validate when `Save()` is called. If validation fails, the `Save()` will fail before the database is updated.