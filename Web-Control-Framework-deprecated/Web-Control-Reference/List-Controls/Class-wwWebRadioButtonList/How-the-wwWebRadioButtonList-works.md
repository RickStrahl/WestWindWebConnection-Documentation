The wwWebRadioButtonList control is a list control that contains a list of radio buttons. Note though the child radio buttons are not RadioButton objects but rather wwWebListItems. 

**Retrieving SelectedValue**  
The control returns its selected value via the SelectedValue property. This property is set during PostBack and automatically reflects the last selections.

To set the SelectedValue you can either assign to the SelectedValue property or set the Selected property of the List item either in the markup/designer or via code.

**List DataBinding**  
The control supports both manual adding of controls or data bound binding.

To manually bind (which is also what happens with markup):

```foxpro
loCtl = this.RadioButtonList

loItem = CREATE("wwWebListItem")
loItem.Text = "Red Item"
loItem.Value = "Red"
loItem.Selected = .T.

loCtl.Items.Add(loItem.Value, loItem)
```

Alternately you can bind to a data source:

```foxpro
loCtl = this.RadioButtonList

Select color, colorName from Colors into TColors

loCtl.DataSource = "TColors"
loCtl.DataTextField = "ColorName"
loCtl.DataValueField = "Color"
```