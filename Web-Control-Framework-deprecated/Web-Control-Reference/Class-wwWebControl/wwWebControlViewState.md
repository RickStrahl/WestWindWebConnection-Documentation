ViewState is a collection of state values that are specific to a given control. Each control has its own ViewState and there's a 'global' viewstate on the Page object.

ViewState is page based state that is encoded and sent back to the client on each request. It is encoded (base64) and reassigned when the page is POSTed back to the server. You can use the Control's ViewState object to assign viewstate or you can call PreserveProperty() which saves a specific property and automatically restores it once.

Any value stored in ViewState is automatically persisted until you explicitly clear it.

**To save:**  
```foxpro
Page.ViewState.Add("FilterString",lcFilter)
```

**To retrieve:**  
```foxpro
lcValue = Page.ViewState.Item("FilterString")
IF ISNULL(lcValue)
    lcValue = ""
ENDIF
```