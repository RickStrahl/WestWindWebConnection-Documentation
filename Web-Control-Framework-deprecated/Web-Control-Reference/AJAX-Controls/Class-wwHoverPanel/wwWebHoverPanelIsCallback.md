Determines whether this control is in a callback if the call is going back to the same page.

This property is useful if you are making callbacks to the current page and you want to handle the callback as opposed to handle normal processing. Typically you'll handle the callback, use Response.Write() to output the HTML and then call Response.End() to complete the request.

In OnLoad():

```foxpro
IF this.MyPanel.IsCallback
    this.ShowCustomerOrders()
    RETURN
ENDIF
```

Then the actual handler function talks to the invisible wwWebDataGrid that lives on the page:

```foxpro
FUNCTION ShowCustomerOrders

SELECT InvNo,InvDate,InvTotal FROM Orders ;
	ORDER BY InvDate DESC ;
	INTO CURSOR TQuery

this.dgOrders.Visible = .T.
this.dgOrders.DataSource = "Tquery"
this.dgOrders.DataBind()

lcHtml = this.dgOrders.Render()

Response.Write(lcHtml)
Response.End()

ENDFUNC
```