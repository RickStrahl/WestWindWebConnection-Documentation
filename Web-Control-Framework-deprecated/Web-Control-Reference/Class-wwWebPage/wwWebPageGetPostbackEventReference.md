Returns a client side Postback Event link (__doPostBack() call) that can be used to fire a server side event from client code.

A common way that this function would be called is from a Grid Column Expression. Here's an example:

```html
<ww:wwWebDataGridColumn runat="server" ID="WwWebDataGridColumn1" 
      Expression="HREF( this.Page.GetPostbackEventReference('Page','DeleteCompany',Trans(pk),.t.) ,[Delete])" 
      HeaderText="Action" >
```

This code uses the HREF() expression to generate a hyperlink and the link itself is set by the GetbackEventReference method. The output from this function generates something like this:

```html
__doPostBack('Page','DeleteCompany','10');
```

Actually the output in this case is used for a HyperLink so the final parameter llIsLink is passed as .T. which prefixes the *javascript:* text to the link so the actual output is:

```html
javascript:__doPostBack('Page','CustomerDeleted','10');
```

The latter is required only if you embed the link into an HREF expression. If you call this code from elsewhere (say in a JavaScript function) the javascript prefix is not required.

Remember if you manually use __doPostback() from script code, you have to ensure that this.Page.RegisterPostbackScriptCode was called somewhere in your code to ensure the postback script is included in the page source.

### Other ways to use this method
You will frequently want to assign this method dynamically for navigation expressions. For example you might do the following in a Repeater:

```html
<ww:wwWebImageButton runat="server" ID="btnDelete" 
                    Click="btnDelete_Click"
                    ImageUrl="~/images/remove.gif"
                    UrlControlSource="this.Page.GetPostbackEventReference('Page','btnDelete_Click',TRANS(pk),.T.)"
/>
```

You can then handle this event as follows inside of the page:

```foxpro
FUNCTION btnDelete_Click()

* ** Retrieve the event parameter (same as: Request.Form("__EventParameter") )
lcId = this.GetPostBackEventParameter()

* ** Do something with the value
this.oEntry.Delete( VAL(lcId) )
ENDFUNC
```