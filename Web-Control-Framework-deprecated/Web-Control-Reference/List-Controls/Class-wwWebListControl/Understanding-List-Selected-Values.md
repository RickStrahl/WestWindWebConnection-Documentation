### DataBound List Items
The most common way you will bind list controls is via binding to a cursor or an array. In this scenario the control does not actually receive a set of list items .rather the 


### Manually added ListItems


You can manually add list items to a list control at design time:

```html
<ww:wwWebListBox ID="lstList" runat='server'>            
    <asp:ListItem value="1">Item1</asp:ListItem>
    <asp:ListItem Value="2">item2</asp:ListItem>
    <asp:ListItem Value="3" Selected="True">Item3</asp:ListItem>                
</ww:wwWebListBox>
```

or by manually adding items to the control with code like this:

```foxpro
this.lstList.AddItem("4","Item 4")
this.lstList.AddItem("5","Item 5")
```

FUNCTION btnSave_Click()

lcVal = this.lstList.SelectedValue
loListItem = this.lstList.Items.Item(lcVal)
this.ErrorDIsplay.ShowMessage("Selected item: " + loListItem.text)

ENDFUNC