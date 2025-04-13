Name Value Collection of static list items that are either added through markup (<asp:listitem />) or by calling the AddItem() method explicitly.
**Important:** Databound items from the DataSource don't show up in the Items collection - these values are rendered directly from their datasource into HTML to reduce overhead.

Items can be added from markup:

```html
<ww:wwWebListBox ID="lstList" runat='server'>            
    <asp:ListItem value="1">Item1</asp:ListItem>
    <asp:ListItem Value="2">item2</asp:ListItem>
    <asp:ListItem Value="3" Selected="True">Item3</asp:ListItem>                
</ww:wwWebListBox>
```

or via code:

```foxpro
this.lstList.Items.AddItem("4","Item 4")
this.lstList.Items.AddItem("5","Item 5",.T.) && selected
```

If values are available in the Items collection you can find values like this:

```foxpro
FUNCTION btnSave_Click()

lcVal = this.lstList.SelectedValue
loListItem = this.lstList.Items.Item(lcVal)
this.ErrorDIsplay.ShowMessage("Selected item: " + loListItem.text)

ENDFUNC
```

The key to the Items collection is the value string.