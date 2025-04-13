Loads the `.oData` member with a record accessed by PK. This method or a specialized version of it should be used to load with record level objects and return them to the caller.

You can overload this method to allow object nesting and auto-loading of sub objects. For example, an oInvoice object may contain an oCustomer object. From `oInvoice.Load()` you can call `oInvoice.oCustomer.Load()` to automatically load the related customer record so the client application never has to explicitly load the customer record.

The default behavior of Load is to do a `SCATTER NAME` by running a SQL statement to retrieve all the data from a record (`SELECT *`) using the PK as the key. If you want you can override this behavior in a variety of ways:

**Create a new object and use it for the oData member**
You can simply override Load to use your own object and populate the data of its members as you see fit. Heck, you can SCATTER from a completely different table if that makes sense.

```foxpro
oInvoice.oData = CREATEOBJECT("cMyCustomData")
oInvoice.oData.Company="westwind"
```

Just be careful when you save to a SQL datasource - the fields in the object must match the fields in the database if you use the autoupdate features. But if you use a custom object you most likely will handle updates to the datasource manually anyway.

**Run a custom SQL statement and do SCATTER NAME of that**
```foxpro
FUNCTION Load
LPARAMETER lnPK

THIS.SetError()

IF EMPTY(lnpk)
   THIS.SetError("Load failed - no key passed.")
   RETURN .F.
ENDIF

IF !THIS.OPEN()
   RETURN .F.
ENDIF
DO CASE
 CASE THIS.ndatamode = 0
      lckey = "PK"

      * **OVERRIDDEN SQL STATEMENT      
   	  SELECT wwmb_Messages.*,wwmb_Forums.ForumName,wwmb_Boards.Path ;
	      FROM wwmb_messages, wwmb_forums, wwmb_boards ;
	      WHERE wwmb_Messages.PK = lnPK AND ;
	            wwmb_Messages.ForumPk = wwmb_Forums.PK AND ;
	            wwmb_Messages.BoardPk = wwmb_Boards.pk ;
	      INTO CURSOR TQuery

      IF _TALLY > 0
         SCATTER NAME THIS.oData MEMO
         THIS.nUpdateMode = 1 && Edit
      ELSE
         SCATTER NAME THIS.oData BLANK
         THIS.SetError("GetRecord - Record not found.")
         RETURN .F.
      ENDIF
   CASE THIS.ndatamode = 2

		THIS.oSQL.cSQL = ;
			[SELECT wwmb_Messages.*,wwmb_Forums.ForumName,wwmb_Boards.Path]   +;
			[   FROM wwmb_messages, wwmb_forums, wwmb_boards]   +;
			[   WHERE wwmb_Messages.PK = ] + TRANSFORM( lnPK ) + [AND]   +;
			[         wwmb_Messages.ForumPk = wwmb_Forums.PK AND]   +;
			[         wwmb_Messages.BoardPk = wwmb_Boards.pk]   


      lnResult = THIS.osql.Execute()
      IF lnResult # 1
         THIS.SetError(THIS.osql.cErrorMsg)
         RETURN .F.
      ENDIF

      IF RECCOUNT() > 0
         SCATTER NAME THIS.oData MEMO
         THIS.nUpdateMode = 1 && Edit
      ELSE
         SCATTER NAME THIS.oData BLANK MEMO
         THIS.SetError("No match found.")
         RETURN .F.
      ENDIF
ENDCASE
```

As you can see here I'm scattering from a custom SQL statement rather than using the stock that would access only the local table. Note that if you use a SQL backend you should use the cSkipFieldsForUpdates property to filter out the fields that are not part of the primary datasource in order to have the auto-save features work correctly as they generate a SQL UPDATE/INSERT statement from the object property signature.