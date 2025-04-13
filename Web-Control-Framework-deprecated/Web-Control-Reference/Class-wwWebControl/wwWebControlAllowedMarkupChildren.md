A comma delimited list of element names that are allowed as child elements of this control and can be used without specifying runat="server" or the full wwWeb<ControlClass> name.

This property is used for controls to notify the parser of what 'unconventional' element names to expect when parsing child control content. 

Examples of controls that use this feature are the list control (for <ListItems> and <asp:ListItem> both of which can be specified without runat="server") and <ItemTemplate> on a Repeater. For example the Repeater control defines this list:

```foxpro
AllowedMarkupChildren = "itemtemplate,headertemplate,footertemplate"
```

So that a Repeater can be defined like this:

```html
<ww:wwWebRepeater runat="server" id="repList">
   <HeaderTemplate>
      Header Text<hr />
   <HeaderTemplate>
   <ItemTemplate>
	Company: <%= Company %><br>
     CareOf: <%= CareOf %>
     <p />
   <ItemTemplate>
   <FooterTemplate>
	<hr />	
   </FooterTemplate>
</ww:wwWebRepeater>
```

Notice that HeaderTemplate and ItemTemplate do not have a runat="server" attribute nor use the wwWebHeaderTemplate prefix.

This functionality is provided for control developers and this functionality is required for any control that is to be contained in the parent container with special naming syntax. Each item that is found (ie. ItemTemplate) is added to the container with AddControl.