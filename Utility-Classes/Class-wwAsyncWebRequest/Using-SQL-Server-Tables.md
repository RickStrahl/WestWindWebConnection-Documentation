There's a wwSQLAsyncWebRequest method that can be used with SQL Server tables instead of Fox Tables. To utilize SQL Server tables you need to:

* Run the West Wind Management CONSOLE and use the Create SQL Server Tables option to create the SQL schema for the Aysnc Event table
* Use the wwSQLAsyncWebRequest class to create your object
* Use the Connect() method to assign either an existing wwSQL object/connection or pass a SQL connectionstring.

In the following example, the Web Connection server has been set up with a SQL connection in the  oSQL member (you can do this simply by setting the WWC_USE_SQL_SYSTEMFILES in WCONNECT.H to .T. and specifying the connection string in the application's INI file and the SQLConnection key).


```foxpro
loAsync = CREATEOBJECT("wwSQLAsyncWebRequest")
loAsync.Connect(SERVER.oSQL)
```

Alternately you can connect on every hit by using a connection string:

```foxpro
loAsync = CREATEOBJECT("wwSQLAsyncWebRequest")
loAsync.Connect("dsn=wwstore_new;uid=sa;pwd=")
```