The wwSQL class manages SQL connections for you by wrappering the SQL Handle into a class property. By default the behavior is to keep the connection open after a SQL command was executed. 

### Connection Defaults
A full connection string looks like this:

```foxpro
oSql.Connect("driver={sql server};server=(local);database=webstore; integrated security=true;")
```

but you can provide a shorter string if you're using any of the default settings. For example the above connection can be changed to:

```foxpro
oSql.Connect("database=webstore")
```

The other values are defaulted.

### Explicitly closing a Connection
The connection is closed only if you explicitly call the Close() method or if you release the reference to the wwSQL object. So if you do this in code:

```foxpro
LOCAL oSQL as wwSQL
oSQL = CREATEOBJECT("wwSQL")
? oSql.Connect("database=webstore")
oSql.execute("select * from wws_customers")
```

The connection will be opened and closed automatically because the oSql object goes out of scope. 
If you hang on to the oSQL reference - by storing it to an object that persists, or by making it PRIVATE/PUBLIC - then the connection remains open until the object reference is released.

### Automatic Reconnection on Connection Failures
If you use connections that are kept open wwSQL can trap connection failures for you and then try to reconnect. So if you have a connection to SQL Server, and SQL Server goes down the current request will fail. If you later come back and the connection is back up wwSQL will try using the existing connection and if that connection doesn't work it will detect the failure and try to reconnect using the same connection string used in the Connect statement (or the cConnectString property more accurately).

The following example demonstrates this sequence of events:
```foxpro
DO WCONNECT

*** Make sure SQL is running
CLOSE data
oSQL = CREATEOBJECT("wwSQL")

? oSql.Connect("driver={sql server};server=(local);database=webstore")

oSql.execute("select * from wws_customers")
? oSql.cErrorMsg
WAIT WINDOW "Turn off SQL Server to test a connection failure"
BROWSE

*** Stop Sql Server - this will fail
CLOSE DATA
IF oSql.execute("select * from wws_customers where company like 'W%'") = -1
   WAIT WINDOW "Connection failed: " + CHR(13)+ oSql.cErrorMsg + ;
           "Now Turn SQL Server back on"
ELSE
   BROWSE
ENDIF

*** TURN SQL SERVer back on - this should work
IF oSQL.Execute("select * from wws_invoice") = -1
   WAIT WINDOW "Connection failed: " + CHR(13)+ oSql.cErrorMsg
ELSE
   BROWSE
ENDIF
```

There's nothing that  that prevents failures of current SQL commands if a connection fails. So if the connection is failing wwSQL tries to reconnect once and then returns an error. The reconnection is meant to detect when a connection comes back up, so it's still vital that your code check result codes from all SQL commands to handle possibly retrieval failures.