Creates a new oData member. Creates a PK value and sets any defaults coded in the New method.

Overload this method to set default values on the oData object properties. 

```foxpro
***Create new object
IF !DODEFAULT()
   RETURN .F.
ENDIF

* **Set default values
THIS.oData.FromName = "Unknown"
THIS.oData.SendTo = "All"
THIS.oData.ThreadId = SUBSTR(SYS(2015),2)
THIS.oData.MsgTime = DATETIME()

RETURN .T.
```