Runs a SQL statement specified in cSQL or passed in as a parameter. This method returns the SQL error code, as well as error information in cErrorMsg, cErrorMsg2 and aErrors. Any connection errors are retried several times.

```foxpro
loSql = CREATEOBJECT("wwSQL")

loSQL.Connect("server=.;database=webstore;integrated security=true")

lnCount = loSql.Execute("select * from wws_items","TQuery") 
IF (lnCount < 0)
   ? "Error: " + loSql.cErrorMsg
   RETURN
ENDIF

browse nowait && Tquery
```