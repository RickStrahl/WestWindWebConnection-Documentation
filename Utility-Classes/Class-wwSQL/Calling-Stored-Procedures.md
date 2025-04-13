Stored procedures can be called in several different ways with wwSQL. You can use either syntax that uses the AddParameter() method to add parameters to add named parameters to your SQL queries or you can use standard VFP SQL Passthrough syntax using `?varname` in a string.

Using the AddSqlParameters() is the preferred mechanism however, since it ensures that the parameter values you are passing are actually in scope when the method is executed as the values are copied into an internal array. Otherwise if you're using `?varname` syntax make sure the variables you use are `PRIVATE` so they are visible inside of the wwSql method that executes the query.

Let's take the following dummy stored procedure:

```sql
CREATE PROCEDURE test 
  @cName varchar(30),
  @iResult int OUTPUT
AS

select * from wws_customers where company like @cName

Set @iResult = 10
GO
```

This SP takes a couple of parameters one of which is an output parameter and it returns a result cursor.

The official way to do this is the following syntax:

```foxpro
oSQL = CREATEOBJECT("wwSQL")  && ODBC
oSQL.Connect("database=WebStore")

*** Add named parameters
oSQL.AddParameter("W%","cName")
oSQL.AddParameter(0,"iResult","OUT")  && Output parameter

*** Make the SP call against the server
IF !oSQL.ExecuteStoredProcedure("test")
      ? oSQL.cErrorMsg
	RETURN
ENDIF

*** Retrieve a parameter
lnResultValue = oSQL.oParameters["iResult"].Value

*** Cursor returned from the SELECT
BROWSE
```

The above is the most effective way and gives you the most control over parameter types. You can actually specify each parameters type and width, size etc.

### Using traditional VFP SQL Passthrough Syntax
We recommend you use the above syntax but for backwards compatibility you can also use Visual FoxPro SQL Passthrough syntax with Execute or ExecuteNonQuery when using wwSQL.

```foxpro
cName = "W%"
iResult = 0
oSQL.Execute("execute Test ?cName,?@iResult")

*** Retrieve parameters
? iResult
? o.Parameters["iResult"].Value
```

Note the @iResult parameter in the SQL string, which signifies that he parameter is an OUT parameter.