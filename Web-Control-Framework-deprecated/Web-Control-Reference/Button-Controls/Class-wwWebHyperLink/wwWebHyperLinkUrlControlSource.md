Controlsource that applies a databinding expression against the NavigateUrl.

This property is especially useful for binding page level post back events that cause a postback to a page event. The following example is used inside of a Repeater to delete an item and the PK is passed as an event  'parameter':

```html
<ww:wwWebHyperLink runat="server" ID="btnDelete" 
                     Click="btnDelete_Click"
                     Text="Delete"
                     CssClass="hoverbutton"
                     UrlControlSource="this.Page.GetPostbackEventReference('Page','btnDelete_Click',TRANS(pk),.T.)"
/>
```

To handle this event the event is fired and it can pick up the __EventParameter POST variable for state:

```foxpro
FUNCTION btnDelete_Click()

lcId = Request.Form("__EventParameter")

IF !this.oEntry.Delete( VAL(lcId) )
	this.ErrorDisplay.ShowError("Couldn't delete entry:<br>" + this.oEntry.cErrormsg
	RETURN
ENDIF	

this.ErrorDisplay.ShowMessage( "Entry deleted." )

ENDFUNC
*   btnDelete_Click
```

Note that you need to call DataBind() on this control (or its host data control like a Repeater/DataGrid) in order for this expression to be applied.