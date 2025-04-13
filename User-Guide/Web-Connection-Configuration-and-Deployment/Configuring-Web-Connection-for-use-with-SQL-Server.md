Web Connection by default works with Visual FoxPro data, but since the Visual FoxPro language is used you can of course access SQL Server directly from your Web Connection using either Remote Views or SQL Passthrough. You can do this easily from within the wwProcess class methods that handle your application code.

### Support for logging and Sessions to SQL Server
However, internally Web Connection also accesses VFP tables for logging and the wwSession functionality. You can also set up logging and session activity to be directed to SQL Server by using the [Create SQL Server Tables Wizard](vfps://Topic/Create%20SQL%20Server%20Tables). The Wizard can set up the log and session tables for you and points you to additional manual configuration steps required for this setup in the help file.

#### Sql Server Configuration Script - `westwind.sql`
You can find the script to create the required Log, Session and Aysnc Request tables and store procedures in `Templates\westwind.sql`. Note that the structure to those tables might change, between versions in which case you should drop the tables and re-run the script.

### wwSQL class wraps SQL Passthrough for error handling and connection management
Web Connection also includes a [wwSQL](vfps://Topic/Class%20wwsql) class which is a wrapper around SQL Passthrough that works for any ODBC connection. This class manages SQL connections in form of an object with methods like Connect and Execute to manage running SQL passthrough operations. The wrapper provides error handling for requests (using Web Connection's familiar lError and cErrorMsg properties) and connection management and re-connection on errors that occur on the connection.

Typical Web applications talking to a SQL backend will want to maintain a permanent connection to the SQL backend. This is easily accomplished by using a wwSQL object instance and attaching it to the wwServer object. The logging and Session features do this using a special oSQL property which is available, but you can also use AddProperty to create a custom object:

```foxpro
FUNCTION SetServerEnvironment
...
THIS.AddProperty("oSQLConn",CREATEOBJECT("wwSQL"))
THIS.oSQLConn.Connect("DSN=Pubs;uid=sa")
...
ENDFUNC
```

Once this object is initialized it can be easily accessed in the application's wwProcess methods:

```foxpro
FUNCTION SQLTest

Server.oSQLConn.cSQLCursor = "TResult"
Server.oSQLConn.Execute("Select * from Authors")
IF Server.loSQLConn.Error
    THIS.ErrorMsg("SQL Error occurred",Server.oSQLConn.cErrorMsg)
    RETURN
ENDIF

*** Display an HTML table from SQL result
Response.HTMLHeader("SQL Demo")
Response.ShowCursor()
Response.HTMLFooter()
```

If you are sending logging and session data to your database as well you can use the Server.oSQL (wwProcess::oServer::oSQL) property to keep everything on a single connection. If you have your own SQL objects and would like have the Web Connection logging and session features share the connection to your existing database you can assign the oSQL.nSQLHandle and oSQL.cConnectString properties once the connection has been made. This will allow Web Connection to reuse your existing connection without requiring an additional connection.

wwSQL is a very simple object - it doesn't help with proper design of a SQL application, but I would recommend using it or a wrapper like it to handle SQL connection errors which is messy to deal with in mainline code. wwSQL is fairly lightweight and supports all that SQLPassthrough provides.