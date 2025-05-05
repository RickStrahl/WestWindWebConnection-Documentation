`wwBusinessObject` supports SQL Server natively for all the implemented base methods, so you can switch data sources on the fly as long as the SQL tables exist.

### SQL Server Configuration
As of Web Connection 6.0 there is no explicit configuration if you use the [Business Object builder](VFPS://Topic/_0GZ1C88OH). When you run the Wizard to create a new business object the required ID table and stored Procedure are automatically created as part of the configuration process.

If you want to manually create the required components you can do so with the following SQL in your target database:

```sql
-- create the id table
CREATE TABLE [dbo].[wws_id](
	[tablename] [varchar](50) NOT NULL,
	[id] [int] NOT NULL,
	[pkfield] [varchar(50)] NOT NULL CONSTRAINT [df_wwsid_pkfield] DEFAULT '',
	[pkwidth] [int] NOT NULL CONSTRAINT [df_wwsid_pkwidth] DEFAULT 0,
	[pktype] [varchar(5)] NOT NULL CONSTRAINT [df_wwsid_pktype] DEFAULT ''
) ON [PRIMARY]


-- create the stored procedure that creates new ids
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

### Upsizing your data
Hey, that's your responsibilility. Create a SQL Server database first.The FoxPro upsizing Wizard is actually quite good and there's an even <a href="https://github.com/VFPX/UpsizingWizard" target="top">better version of the Upsizing Wizard available on VFPX</a>. Alternately you can of course create your tables on the server manually. Remember that you need to have an integer PK field. For more info on these steps see the SQL Server documentation and the docs for VFP's upsizing Wizard.

### Setting up the business object using the Wizard
The easiest way to create new business objects is to use the [Business Object Wizard](VFPS://Topic/_0GZ1C88OH).

![](/images/misc/BusObjectBuilder.png)


For the Fox data paths just put in the filename if you won't have fox data files. Check the SQL data option and put in a connection string such as:

```
driver={sql server};server=(local);database=YourDataBase;uid=sa;pwd=;
```

The name of the ID table doesn't matter for the SQL backend as it always uses wws_id per the stored procedure.
If you don't have Fox data uncheck the Fox Data box. 

When the wizard runs it will create the ID table if one doesn't exist already, create an entry for your table that you are mapping to the business object and set the PK count to the highest value found in that table. It will also set the tablename, connection string and set the datamode to run in SQL Server (2).


Alternately you can also manually create your business object classes - you simply subclass `wwBusinessObject` and set the appropriate properties to specific where and how the data is to be accessed. Here's a simple business object:

```foxpro
DO wwBusinessObject
DO wwSql

SET PROCEDURE TO ccustomer.prg ADDITIVE



*************************************************************
DEFINE CLASS ccustomer AS wwbusinessobject
*************************************************************

cAlias = "customers"
cDataPath = ""
cFileName = "customers"

cPkField = "id"
cIdTable = ".\cust_id"

*** Sql Connection String
cConnectString = "database=test"

*** 0 - fox 2 - Sql 4-Web
nDataMode = 0


ENDDEFINE
```

You can then start adding your own custom functionality.  Note that the Wizard generates a few additional methods for creating and reindexing tables (fox only), but that's purely optional anyway. If you want to see what that looks like use the Wizard to generate a new test class and check the output.

### Test operation**  
Assuming your database has data in it you should now be able to run against your SQL data with the following commands:

```foxpro
DO wconnect
oDev = NEWOBJECT("wwDevRegistry","test.vcx")
? oDev.Query()
? oDev.Load(1)   && .T.
? oDev.oData.LastName
oDev.oData.LastName = "Rick " + SYS(2015)
? oDev.Save()  && .T.
```

### Handling SQL access in your Web application**  
This works and is nice and easy, but I would recommend you use an explicit connection. The code above will set a connection and reset the connection when the object goes out of scope. You can persist a connection by using an explicit oSQL object reference:

```foxpro
oSQL = CREATE("wwSQL")
oSQL.Connect("driver={sql server};server=(local);database=wwDeveloper;uid=sa;pwd=;")

oDev = NEWOBJECT("wwDevRegistry","test.vcx")
oDev.SetSQLObject(oSQL)
? oDev.Query()

oCust = NEWOBJECT("wwCustomer","test.vcx")
oCust.SetSQLObject(oSQL)
? oCust.Query()
? oDev.Query()
```

In this example both objects share the same connection.

In a Web Connection application you probably want to declare your oSQL member and attach it to the Server object. You can do this in the SetServerProperties method:

```foxpro
*** Web Store - SQL Version
#IF WWSTORE_USE_SQL_TABLES
  THIS.owwStoreSQL = CREATEOBJECT("wwSQL")
  IF !THIS.owwStoreSQL.Connect(THIS.oConfig.owwStore.cSQLConnection)
     THIS.owwStoreSQL = .NULL.
     WAIT WINDOW TIMEOUT 5 "Web Store SQL Connection failed to load!"
  ENDIF
#ENDIF
```

In your Process methods you can then do:

```foxpro
oCust = NEWOBJECT("wwCustomer","test.vcx")
oCust.SetSQLObject(Server.owwStoreSQL)
```

The connection remains alive across hits in this scenario, because the server object stays alive indefinitely.