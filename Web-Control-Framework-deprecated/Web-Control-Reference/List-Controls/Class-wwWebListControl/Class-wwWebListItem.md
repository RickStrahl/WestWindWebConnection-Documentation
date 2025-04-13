ListItem object used to add items to a List Control. Used primarily as an internal object to read listitems from script pages.

**Script Code:**  
```html
<ww:wwWebDropDownList ID="lstColors" runat="server" Width="200">
    <asp:ListItem>Reddish</asp:ListItem>
    <asp:ListItem Value="Blue">Blueish</asp:ListItem>
    <asp:ListItem Value="Green">Greenish</asp:ListItem>
</ww:wwWebDropDownList>
```

**Fox Code:**  
```foxpro
THIS.lstColors = CREATEOBJECT("wwwebdropdownlist",THIS)
THIS.lstColors.Id = "lstColors"
THIS.lstColors.Width = [200]
THIS.AddControl(THIS.lstColors)

_1N0116E4D = CREATEOBJECT("wwWeblistitem",THIS)
_1N0116E4D.Id = "_1N0116E4D"
_1N0116E4D.Value = [Blue]
_1N0116E4D.Text = [Blueish]
_1N0116E4D.UniqueId = [lstColors__1N0116E4D]
THIS.lstColors.AddControl(_1N0116E4D)

_1N0116E4F = CREATEOBJECT("wwWeblistitem",THIS)
_1N0116E4F.Id = "_1N0116E4F"
_1N0116E4F.Value = [Green]
_1N0116E4F.Text = [Greenish]
_1N0116E4F.UniqueId = [lstColors__1N0116E4F]
THIS.lstColors.AddControl(_1N0116E4F)
```