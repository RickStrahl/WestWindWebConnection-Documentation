The Web Control framework handles events in two ways:
<ul>
* **Buttons**  
 Buttons are special objects and they automatically post back to the server. If no event target is found Web Connection runs through all buttons and checks to see if the button was clicked.

* **__doPostBack() Client Script**  
Anything but stock buttons that posts back to the server uses some special client script to post events from the client to the server by writing out event information into a few hidden variables and submitting the form.
</ul>

### Firing a Button from the Client
Button clicks is completely automatic and built into both HTML and Web Connection natively. There's nothing special about triggering clicks - they just automatically fire events on the server. If you want to trigger a button to post back to the server the easiest is simply to call the specific button's 'click' method in JavaScript code:

```html
<script type="text/javascript">
function ScriptCode()
{
    document.getElementById('btnSubmit').click();
}
</script>
```

This fires the click of the button and simply submits it. Easy.

### Firing a custom Event from the client
But buttons are usually not the only target of explicitly triggered events from the client. A more common scenario is that you click an item in a datagrid and want to have it fire an event on the server that doesn't navigate to a new page and instead handles the operation on the current page. 

Firing events from the client is straight forward. Let's take a TextBox and attach to the onblur event (focus lost) and fire it into the page as a POSTBACK. 

```html
<ww:wwWebTextBox runat="server" id="txtName"  onblur="__doPostBack('Page','ontxtName_LostFocus')" />
<ww:wwWebLabel runat="server" id="lblNameEcho"></ww:wwWebLabel>
```

This says when onblur fires, submit the form and fire the OnTxtName_LostFocus event on the Page object. 'Page' in this case is a fixed name (you can use 'Page' or 'this'), but you can specify the unique ID for any control on the page and point at any method of the control.

Next we need to implement the event:

```foxpro
DEFINE CLASS Absolute_Page as WWC_WEBPAGE

FUNCTION OnLoad()
this.RegisterPostbackScriptCode()
ENDFUNC

FUNCTION OnTxtName_LostFocus()
this.lblNameEcho.Text = this.txtName.Text + ". " + TIME()
ENDFUNC

ENDDEFINE
```

Note the call to RegisterPostbackScriptCode() - this ensures that the __doPostback script handler is available on the page and is required when you manually add events on the client.

### A more complex Scenario: Firing DataGrid Item events
Let's look at a more complex example. You have a grid display and have a Delete button on the grid. When you click Delete you want to post back to the current form delete the selected record and then redisplay the grid without the deleted record.

Let's look at the example:

```html
<ww:wwWebDataGrid ID="gdCustomers" runat="server">
<Columns>
    <ww:wwWebDataGridColumn runat="server" ID="Company" Expression="Company" >
    </ww:wwWebDataGridColumn>
    <ww:wwWebDataGridColumn runat="server" ID="WwWebDataGridColumn1" 
                            Expression="this.Page.DeleteExpression()" 
                            HeaderText="Action" >
    </ww:wwWebDataGridColumn>
</Columns>
</ww:wwWebDataGrid>
```

The second column is a Delete link. I'm using an Expression that points back to the page to create the output for this column which is going to be a hyperlink that calls the javascript:__doPostBack() function. I'm using an expression because there will be embedded expressions in the string and it's easier to write in VFP than in the VS.NET editor.

```foxpro
FUNCTION DeleteExpression()
RETURN [<a href="javascript:__doPostBack('Page','DeleteCompany','] + TRANSFORM(pk) + [');" >Delete</a>]
ENDFUNC

FUNCTION DeleteCompany
lcResult = Request.Form("__EventParameter")

* ** Delete Code would go here
this.Errordisplay.ShowMessage("you've selected " + lcResult ) 
ENDFUNC
```

I created two methods in the Page class. The first generates the Expression to display and as you can see it creates the __doPostBack() function call for each link in the grid. It says:
<ul>
* Call the Page Class
* Fire the DeleteCompany method
* Pass the PK as a value to the method
</ul>

The actual HTML generated inside of the grid column looks like this:

```html
<a href="javascript:__doPostBack('Page','DeleteCompany','32');" >Delete</a>
```

Note the special name 'Page' in this example. You can use Page or This to specify that you want to fire an event on the Page object. For any other control use its UniqueID value to identify the control (ie. this.txtButton.UniqueID).

When clicked this code now goes out and fires the Page.DeleteCompany method in the page. The mthod then can use Request.Form("__EventParameter") to retrieve the parameter that the client sent. So at this point you'd be ready to delete the record with a PK value of 32. The code above merely echos back the value passed. 

Note parameters are always passed and retrieved as strings.