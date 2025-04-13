A Web Control is an object that has properties which are then rendered into HTML by the framework. The advantage of a control is that your code can simply set properties and you can have a single optimized HTML generation routine that turns the control property settings into HTML consistently. 

If you create controls in script tags, any control properties that are found in the script markup for a control are mapped to the property. So if you have a textbox with:

```html
<ww:wwWebTextBox runat="server" id="txtName" Text="Rick" 
                 Width="200px" onchange="CheckValidity()"/>
```

Web Connection parses the script and assigns Id, Text, Width to the matching properties of the control. It gets turned into:

```foxpro
loCtl = CreateObject("wwWebTextBox")
loCtl.ID = "txtName"
loCtl.Text = "Rick"
loCtl.Width = "200px"

*** Any non-property/method attributes are added to the Attributes
loCtl.Attributes.Add("onchange","CheckValidity")
```

Any non-property expressions (ie. any custom Attributes) are 

In addition to properties any control also works with custom attributes that are assigned either in script page content or via code. In the TextBox example onchange is not a property or method on the control so it is mapped to a custom attribute and added to the Attributes collection which is rendered as is into the HTML. Anything that doesn't map to a Property or Method is turned into an attribute and simply added. At render time the attribute is simply rendered as is.

The Attributes collection is also available programmatically from within your code, and you can use it to force new attribute tags onto controls that are not already handled by the control framework.