The text value of the currently selected item in the list control. 

Note that this reflects the Value property of the control - you can read the value on a POSTBACK or set it at anytime during the page cycle, to effectively set the selected value of the list that is displayed highlighted.

To select a selection from the listbox:
```foxpro
lcCustomerId = this.lstCustomers.SelectedValue
```

To set the listbox to show with a value selected:
```foxpro
this.lstCustomers.SelectedValue = user.LastCustomer
```

To get or set multiple selections use the [wwWebListControl::SelectedValues](vfps://Topic/_1MX0QL91Q) collection instead.