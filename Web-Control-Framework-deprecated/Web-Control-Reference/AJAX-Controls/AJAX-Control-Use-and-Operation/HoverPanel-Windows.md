The wwHoverPanel control allows you to create a window with content from a URL, with the result popping ontop of the active Web Page at the current mouse position. Here's what this control looks like in action:

![](IMAGES%5CWebControls%5CControls%5CwwWebAjaxHover.png)

Creating Hover Windows is a very simple process. Typically you'll need to do the following:

<ul>
* Place a wwHoverPanel control on the page 
* Set the ServerUrl to point at an external URL, or leave blank to fire back to the current page
* Create a new Server Page or Page method to create an HTML fragment to display
* Hookup the appropriate event in the client script code (for example, onmouseover or onclick)
* From the script call the MyPanel.callStartCallback(event,querystring) function to initiate
</ul>

Take a look at the AjaxHoverPanel.wcsx and AjaxHoverPanel_Page.prg example which is provided in the WebControl samples.

Start by adding a wwWebHoverPanel control to the page and naming it LookupPanel:

```html
<ww:wwWebHoverPanel ID="LookupPanel" runat="server" 
              EventHandlerMode="ShowHtmlAtMousePosition"
              ServerUrl="~/webcontrols/SmallCustomerForm.wcsx"              
              NavigateDelay="250"
              PostbackMode="Post"              
              ScriptLocation="WebResource"
              CssClass="blackborder"            
              Style="display: none; background: white;" 
              PanelOpacity="0.89" 
              ShadowOffset="8"
              ShadowOpacity="0.18">
</ww:wwWebHoverPanel>
```

This sets up the wwWebHoverPanel control to run the callback and display the result from the callback - the output from SmallCustomerForm.wcsx - in the panel and moves it to the current mouse position. The EventHandlerMode determines how the panel is displayed in this case the default ShowHtmlAtMousePosition is used. Other options include show in Panel which displays the content but doesn't move the mouse or Frames versions of this behavior where the content is displayed in an IFrame.

The other important parameter is the ServerUrl which determines where the callback is sent to on the server. Here I'll go to a separate page and I provide the URL here. Note that this should be a base URL - you can add a querystring later when you initiate the callback to provide parameterization. If you leave this value blank the control calls back to the current page. We'll look at that scenario a little later. For now we'll call an external .WCSX page which renders an HTML fragment which is basically a <div> tag with some embedded HTML.

The ScriptLocation parameter determines where the wwScriptLibrary.js file is loaded from. WebResource is the default and causes a call to Web Connection to provide the file dynamically. Web Connection will automatically GZip compress the library if the browser supports it. Alternately you can specify the location on disk for the file - typically in the /images/ directory of the site.

Finally the PostBackMode is set to Post which means any form variables on the page are posted back to the server. This allows you to reference controls like you normally would on the server (ie. this.txtName.Text). Other option include the more lightweight Get which doesn't post any form data and PostNoViewState which post only the raw form data but not ViewState.

### Hooking up the HoverPanel to a DataGrid for Hovering
Next let's hook up the event handlers in the DataGrid control. What needs to happen is that as we hover over a link we need to pop up display, then when we move off the link hide it again. To do so I'll hook up the onmouseover and onmouseout client side events in the data grid i nthe Company column.

```html
<ww:wwWebDataGrid ID="gdDeveloperList" runat="server" CssClass="blackborder" PageSize="10" Width="800" DataKeyField="Id"> 
<Columns>
    <ww:wwWebDataGridColumn ID="wwWebDataGridColumn1" runat="server" 
                            Expression="this.Page.CompanyColumnExpression()"
                            HeaderText="Company" 
                            Sortable="True" 
                            SortExpression="upper(Company)"
                            ItemAttributeString="style='width:400;'"
    >
    ... additional columns
</Columns>
</ww:wwWebDataGrid>
```

Eh, where's the onmouseout and onmouseover code? Well, it's hidden in a databinding expression function called this.Page.CompanyColumnExpression() which looks like this:

```foxpro
FUNCTION CompanyColumnExpression()

* ** We're binding to Cursor the grid is bound to (Pk,Company fields)
* ** We're hooking up the AJAX callback in onmouseover with the StartCallback function
* ** which is found in the support JS library for the Ajax control.
* ** It says: Start the callback for the LookupPanel control with a querystring attached
* **          to the ServerUrl of id=1 to identify the record we'll want to pop over
RETURN [<a href="BasicDataBinding.wcsx?Id=] + TRANSFORM(PK) + [" ] +;
	   [ onmouseover="LookupPanel.startCallback(event,'id=] + TRANSFORM(PK) + [');" ] +;	   
	   IIF(!this.chkCallbackPage.Checked,[onmouseout="LookupPanel.hide();" ],[onmouseout="LookupPanel.abort();"]) + ;
	   [>] + TRIM(Company) + [</a>]

ENDFUNC
```

All this expression does is create a somewhat complex HTML Href anchor that would have been difficult to directly embed into the HTML as an Expression attribute because of string delimiter limitations. It can be done but it's really difficult to read and to me at least it's easier to write out the string in FoxPro code where we have 3 string delimiters to choose from to build the expression string. 

In case you're wondering the expression creates output like this when run:

```html
<a href="BasicDataBinding.wcsx?Id=5"  
   onmouseover="LookupPanel.startCallback(event,'id=5');" 
   onmouseout="LookupPanel.hide('LookupPanel');" >Conarc, Inc</a>
```

Alternately you can skip generating the expression using the explicit onmouseover/onmouseout links on the link altogether and instead use unobtrusive JavaScript with jQuery by finding all the table's a elements:

```javascript
<script>
$(document).ready( function() {
    $("gdDeveloperList>tbody>tr>td>a").hover(function(e) {
        // find id on row - ID is set based on DataKeyField of Grid
        // with value of id="gdDeveloperList_<idvalue>"
        var id=$(this).attr("id").replace("gdDeveloperList_","");
        LookupPanel.startCallback(e,"id=" + id);
    }, 
    function() {
        LookupPanel.hide("LookupPanel");
    });
});
</script>
```

Effectively the behavior of both of these are identical, but the latter is generally accepted as a cleaner approach in the document.

The key function is:

```javascript
LookupPanel.startCallback(e,"id=" + id);
```

which triggers the actual AJAX callback to the server, retrieves the data and then displays the pop up window with the returned HTML when the callback returns. You can use this code from anywhere on the client - like a button or link - although most commonly you'll see this tied to hover behavior as shown above.

LookupPanel is the name of the wwHoverPanel control, which is generated as a global JavaScript object available to your code. LookupPanel.startCallback() launches the AJAX callback and display operation, while LookupPanel.hide() hides the dialog. These two functions can be fired anywhere in JavaScript code although most commonly they are tied to hover operation.

The startCallback() method requires a DOM event object parameter which is required to figure out the current mouse position to hover under the cursor. The second parameter is optional and is a string that is appended to the ServerUrl declared on the HoverPanel server control. Typically this will be a querystring key/value pair(s) that provide the required state for the server operation that is called - these values act as the parameters to allow the server to return data for the selected context.

On the server then you need to create a page that handles this URL. This sample here is SmallCustomerForm.wcsx. This page is a plain WCSX page, but unlike a standalone page it doesn't have any HTML or BODY tags. Rather it's just an HTML fragment that contains a table with the embedded script tags. Here's an abstract of the header:

```html
<%@ Page Language="C#" 
	ID="SmallCustomerForm_Page"
	GeneratedSourceFile="webcontrols/SmallCustomerForm_Page.prg" 
%>
<%@ Register Assembly="WebConnectionWebControls" 
    Namespace="Westwind.WebConnection.WebControls"
    TagPrefix="ww" 
%>
<!-- 
    This form represents an HTML fragment only not a whole page
    This page is loaded as part of the AJAX HoverPanel demo
-->
<table class="blackborder" width="500" cellpadding="6">
                    <tr>
                    <td colspan="2" class="gridheader" style="font-size:14pt;text-align:center;">
                         <%= this.Page.oDeveloper.oData.Company %>
                    </td>
                    </tr>
                    <tr>
                    <td colspan="2" height="0">
                        <ww:wwWebImage runat='server' ControlSource="this.Page.oDeveloper.oData.Logo" />
                    </td>
... more rows
</table>
```

Note there's no HTML or BODY tag - it's just a fragment. The codebehind of this page is super simple - it basically loads a developer business object and databinds. That's it:

```foxpro
* ** Need Developer object ref so we can use it in
* ** the WCSX markup
oDeveloper = null

FUNCTION OnLoad()

loDeveloper = CREATEOBJECT("wwDevRegistry")

lnID = VAL(Request.QUeryString("ID"))

IF  EMPTY(lnID)  OR !this.oDeveloper.Load(lnId)
   Process.StandardPage("Invalid Developer","Please select a valid customer.")
   RETURN
ENDIF

IF !this.IsPostback
	this.DataBind()
ENDIF

ENDFUNC
```

Ok, we're ready to run now. First make sure SmallCustomerForm.wcsx works. Fire it with a valid customer ID in the browser and it should display a small non-styled display of the customer information. If that works, run the HoverList form and you should be able to hover over any of the hyperlinks in the grid to see the customer popup window now. The popup comes up in the context of the current page now so it is properly styled.

### Calling back to the Current Page
The above example calls to an external - another Web Connection (or other) page. However, you don't have to call another page. If you want to create output in code you can call back to the current page just as easily and hook up the code to a method of the Page class.

Let's add a checkbox to the top of the form, name it chkCallbackPage and set its AutoPostBack to true. Now add this code to your original form:

```foxpro
FUNCTION OnLoad()

IF this.chkCallbackPage.Checked
      * ** Blank ServerUrl means go back to the current page
	this.LookupPanel.ServerUrl = ""
ELSE
	this.LookupPanel.ServerUrl = "~/webcontrols/smallCustomerForm.wcsx"
ENDIF	

* **** Check for callback and if it is go to special rendering
IF this.LookupPanel.IsCallback
	this.ShowCustomerDetail()
	RETURN
ENDIF	**  

* ** Normal display load code for the page - bind the data grid
SELECT * FROM wwDevRegistry ORDER BY Company INTO CURSOR TQuery
this.gdDeveloperList.DataSource = "TQuery"
this.gdDeveloperList.DataBind()

ENDFUNC
```

The code checks for the IsCallback property of the LookupPanel control which is set by the wwWebHoverPanel control if it is in a callback. It knows this because there are special parameters passed via POST data or query string that let the HoverPanel know that it's in a callback. This lets you conditionally branch your code to return a separate response from typical PostBack operations. Above we route to the ShowCustomerDetail method. Again let's generate an HTML fragment but this time by utilizing a built in control the wwWebErrorDisplay control.

```foxpro
FUNCTION ShowCustomerDetail()

lcID = Request.Params("ID")

* ** Let's just generate some simple HTML here
* ** For demonstration use an error control dynamically <g>
loError = CREATEOBJECT("wwWebErrorDisplay")
loError.width = 350

* ** Because this app doesn't run directly off a virtual 
* ** we need to explicitly point the image urls - note /weblog/ path
loError.ErrorImage = "~/webControls/images/warning.gif"
loError.InfoImage = "~/webControls/images/info.gif"

loDeveloper = CREATEOBJECT("wwDevRegistry")
IF !loDeveloper.Load( VAL(lcId) )
	loError.ShowError("Invalid ID passed to server.")
	Response.Write(loError.Render())
	Response.End()  && Stop output
	RETURN
ENDIF

loError.ShowMessage( "<b>" + loDeveloper.oData.Company + "</b><hr>" + ;
					 loDeveloper.oData.Phone + "<br>" +;
					 loDeveloper.oData.Address + "<br>" + ;
					 TRIM(loDeveloper.oData.City)+ ", " + loDeveloper.oData.State + " " + loDeveloper.oData.Zip + "<br>"+;
					 loDeveloper.oData.Country)

Response.Write(loError.Render())
Response.End()
ENDFUNC
```

The output generation here is really simplistic using the ErrorDisplay control to give a nice block control display with minimal effort. How the output is generated is really not relevant, but the end result needs to be an HTML fragment that gets written into the Response object. Finally Response.End() should be called to keep any further output from firing.

Now run the form again - you should be able to trigger between the external page and the same page callback by setting the checkbox at the top. 

Notice that in all of this we didn't write any client side code to make all of this happen with the exception of the StartCallback() call in the OnMouseOver. Is that easy enough for you?

### Using Page Controls to render Callback Output
Here's another easy way to created the HTML Fragment response for HoverPanel results: You can add an invisible control or container (like a Panel) to the page, and render control yourself by making it .Visible and calling its .Render method.

For example you could add a DataGrid to the form and format it as needed to display your data setting up column displays and databindings. Mark Visible=False so it doesn't render under normal conditions. Then when the callback comes in you route the callback to a special method that handles the callback. In that handler method you can run a query and bind the data to the currently invisible DataGrid, make it visible and then call Render() on it. Assume a callback method called ShowCustomerOrders:

In OnLoad():

```foxpro
IF this.HoverPanel.IsCallback
    this.ShowCustomerOrders()
    RETURN
ENDIF
```

Then the actual handler function talks to the invisible wwWebDataGrid that lives on the page:

```foxpro
FUNCTION ShowCustomerOrders

SELECT InvNo,InvDate,InvTotal FROM Orders ;
	WHERE CustId = Request.QueryString("ID")
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

Along the same lines you can create composite content that lives inside of a control like a wwWebPanel that contains multiple labels and data grids or whatever you need. You can simply talk to the control in the panel set their values and eventually just call the .Render() method of the panel to cause the panel to render. This is a very powerful aggregation mechanism that makes it easy to encapsulate the callback handler code into the current page and still take advantage of the Page pipeline.

Note that in some situations - depending on the controls you are running you may have to call OnPreRender() on the control or container you want to render. This is because the OnPreRender() of some controls contain logic that is necessary to render the controls properly. 

Another interesting option to render output is to add another control to the page that is initially invisible. You can add a Panel to the page, and add custom display logic to this panel. For example, you can add a datagrid to the panel and then databind the data grid in the callback. Finally call Render() on the Panel to actually display the output from the panel as you would in a full request.