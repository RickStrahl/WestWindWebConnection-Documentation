By default `wwBusinessObject` works with integer PKs and uses a lookup table to generate new ids as objects are created. The default process works like this:

When you call the New() method:

* New() creates a new blank record and issues a SCATTER NAME on it
* New() calls CreateNewId() to generate a new PK and assigns it to the PK field
* CreateNewId() internally calls a VFP Function or SQL Server Stored Procedure to generate a new Integer PK
* You can override CreateNewId() to return a custom value with your own logic

### How the default New Id Creation behavior works
The key here is the CreateNewId() method which generates the new Integer Pk. This default behavior uses a lookup table which contains a key for the table name and an integer value that is incremented. When a new Id is requested the single record is briefly locked and the value incremented by one and returned.

### FoxPro New Id Creation
In FoxPro this behavior is handled by FoxPro code inside of CreateNewId() using the table defined in the cIDTable property. 

### Overriding New Id Generation
You can easily override or completely remove the ID generation logic. The `CreateNewId()` method is responsible for generating new ids and it returns a result value that is used for the new ID when `CreateNewId()` is called.

To override the ID generation just override the method and return whatever value and type you like. For example to generate new string Ids you might use:

```foxpro
FUNCTION CreateNewId()

*** Built-in
* RETURN SYS(2015)

*** Generate a 12 character Unique ID from wwutils.prg
RETURN GetUniqueId(10)
ENDFUNC
```

The resulting value is assigned to the PK field.

If you want to skip ID generation because your database automatically creates new auto-ids you can `RETURN -1`:

```
FUNCTION CreateNewId()
RETURN -1
```

This skips the assignment part of the new ID generation. Note that you'll be responsible for filling the `.oData.Pk` (or whatever field you use for a PK) if you use that most likely by reloading the record after it's been saved with the generated id.

One advantage of the pre-generation of keys is that you can assign a newly generated key to a relationship that has not been saved to disk yet. 

### Sql Server New Id Creation
For SQL Server a stored procedure `sp_ww_NewId` is used and it relies on a table called wws_id to hold the tables:

```sql
CREATE PROCEDURE [dbo].[sp_ww_NewID]
  @cName char(30),
  @nRetval int OUTPUT
AS
UPDATE wws_Id 
  SET id = id + 1,
      @nRetval = id + 1
WHERE TableName = @cName

if @@ROWCOUNT < 1
BEGIN
	insert into wws_id (Tablename,Id,pkfield,pktype,pkwidth) values (@cName,1,'','',0)
		set @nRetval = 1
END
```

The table and stored procedure can be automatically generated from the Console using the Create Sql Server Tables.

The wws_id table is very simple and looks like this:

```sql
CREATE TABLE [dbo].[wws_id](
	[tablename] [varchar](50) NOT NULL,
	[id] [int] NOT NULL,
	[pkfield] [varchar(50)] NOT NULL CONSTRAINT [df_wwsid_pkfield] DEFAULT '',
	[pkwidth] [int] NOT NULL CONSTRAINT [df_wwsid_pkwidth] DEFAULT 0,
	[pktype] [varchar(5)] NOT NULL CONSTRAINT [df_wwsid_pktype] DEFAULT ''
) ON [PRIMARY]
```

Each table that you want to create a PK for should have an entry in `tablename` field and `id` should start at 0.