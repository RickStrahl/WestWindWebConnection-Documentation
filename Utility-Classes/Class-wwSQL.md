The wwSQL class is a wrapper around SQL Passthrough that provides solid error handling and [connection recovery services](vfps://Topic/_1HO0QRWHD) as well as an easier interface to make SQL passthrough calls. Rather than managing a SQLConnect handle you simply hang on to a class reference.

The class is fairly simple and only performs base tasks, but it exposes the underlying SQL handle so you have full access to all the features of the SQLPassthrough mechanism for setting connection properties etc. This class is mainly geared at making common tasks more manageable.

```foxpro
DO wwSql
loSql = CREATEOBJECT("wwSQL")

loSQL.Connect("server=.;database=webstore;integrated security=true")

*** Query
lnCount = loSql.Execute("select * from wws_items","TQuery") 
IF (lnCount < 0)
   ? "Error: " + loSql.cErrorMsg
   RETURN
ENDIF

browse nowait  && Cursor TQuery

*** Insert, Update, Delete
pcSku = "WCONNECT"
ptTime = DATETIME()
IF (!loSQL.ExecuteNonQuery("update wws_items set expected=?ptTime  where sku=?pcSKU "))
   ? "Error: " + loSql.cErrorMsg
   RETURN
ENDIF

? loSql.nAffectedRecords

*** Auto-closes when object goes out of scope
* loSql.Close()
```