Returns the PostBack event parameter that might have been set by a postback event from the client. 

This operation is a shortcut for:

```foxpro
lcString = Request.Form("__EventParameter")
```

Postback parameters are created as part of postback script created with GetPostBackEventReference - one of its parameters is a state parameter that can be posted back, typically some sort of ID. GetPostBackEventParameter retrieves this parameter on a postback.

The following event handler on a page illustrates:

```foxpro
FUNCTION DeleteCustomer()

lcId = this.GetPostBackEventParameter()

IF !THIS.oCustomer.Delete(VAL(lcId))
	this.ErrorDisplay.ShowError("Couldn't delete customer")
	return
ENDIF
* ** Just redisplay the list
ENDFUNC
```

This code is called from client events generated say in a grid or repeater which looks like this:

```html
<a href="javascript:__doPostBack('Page','DeleteCustomer','270878') ">Delete</a>
```

which is generated from this markup code:

```html
<ww:wwWebDataGridColumn ID="colAction" runat="server"
     Expression="Href(this.Page.GetPostBackEventReference('Page','DeleteCustomer',Trans(pk),.t.),'Delete')"
     style="text-align: center;" HeaderText="Action" />
```

Note the embedded GetPostBackEventReference and the Trans(Pk) which is the parameter embedded in server side generation code.