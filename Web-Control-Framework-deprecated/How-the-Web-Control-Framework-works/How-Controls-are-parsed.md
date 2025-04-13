Controls are parsed as part of the Web page parsing and turned into Visual FoxPro objects that are child objects of the Page. The Parser parses objects by reading their tag name and translating this name into a Visual FoxPro based class match.

By default the naming works like this: If you have a control on a form like this:

```html
<ww:wwWebTextBox runat="server" 
                 id="txtName" Width="90" 
                 onclick="MoreDetail();"/>
```

Web Connection looks for a control named wwWebTextBox which *must be available at the time the page is parsed*. The class is instantiated at parse time, and then properties and events are mapped to the control using PEMSTATUS() to try and match up properties and events. Alternately Web Connection also looks for ASP.NET controls and automatically tries to adjust the name. So:

```html
<asp:TextBox ... />
```

is turned into wwWebTextBox and essentially exhibits the same behavior as the code above.

Properties of the controls are parsed using PEMSTATUS() to match properties and events. If there's a matching property or event it is assigned directly. Any other tags that aren't recognized are parsed into the Attributes collection of the control and rendered as is. So for example, in the tag above, the client side onclick= attribute doesn't match any properties or events on the FoxPro control, so it's added to the Attributes collection, which then renders the onclick directly into the INPUT tag:

```html
<input type="text" name="txtName" style="width:90px" 
       onclick="MoreDetail();" />
```

If you create controls of your own, the process will work exactly the same way and you can simply reference the controls by their name, prefixed with ww:, as long as you make sure that the controls can be instantiated when the parser is run. If a class cannot be found or there's an error you'll get an error message generated into the page like this:

**[Unsupported Control: wwwebunknowncontrol]**  

where the control name is embedded into the brackets. 

Note that the RUNAT attribute is required in order for controls to be recognized as controls. An ID is also required in order for controls to generate accessible names in the page.

### Containership
Controls can be laid out hiearchically, so you can have controls that contain other controls. Examples are Panel or User Control as well as indirect containers like Lists, DataGrids, Repeaters which can contain list items, columns or item templates respectable.

Container controls have the IsContainerControl property set to true and must implement the AddControl method that accepts contained objects of specific types. The default implementation of AddControl accepts any control and simply adds it to the container as content which is how Panels and User Controls work. Other controls like the DataGrid, Lists and Repeaters expect very specific types of objects only and skip everything else.