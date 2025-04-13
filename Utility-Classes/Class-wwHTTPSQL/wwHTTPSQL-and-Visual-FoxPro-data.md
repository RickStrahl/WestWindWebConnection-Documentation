wwHTTPSQL was designed to work with SQL Server on the server side, but it should work equally well with any ODBC data source on the server. It does not work very well with Visual FoxPro data on the server side however due to some limitations on command line execution of SQL commands.

The problem is that VFP command line execution does not support long string literals (255 characters max) which is required to run UPDATE and INSERT statements that are dynamically generated. The only way to pass these types of values that exceed 255 characters to the server is via named parameters. The following demonstrates:

```foxpro
*** This won't work:
pcLongString = replicate('x',257)
oData.Execute([update wwdevregistry set MyMemo = '] + pcLongString + ['])

*** But this will:
oData.AddSQLParameter( "pcLongString",pcLongString )
oData.Execute([update wwDevRegistry set MyMemo = ?pcLongString])
```

This means that just about any memo field you'll update on the Web server requires the use of named parameters.

VFP command lines are also limited to 8k characters, so very large SQL statements or those that contain lots of string literals may fail due to this length.

These limitations affect the wwBusiness class using nDataMode of 4 (Web Access) as it creates INSERT and UPDATE statements based on expanded string literals. If any of these exceed the 255 command limit errors occur.

wwHTTPSQL will return an execution error (SQL Command failed) when command lengths are exceeded.