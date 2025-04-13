Creates an HTML Listbox. The listbox can be populated from a data source of a cursor, array or collection and by binding an evaluated expression for text and value displays of each list item.

```html
<%= HtmlListBox("lstCustomers",TRANSFORM(poModel.Pk),"TT_Cust",
                "TRANSFORM(Pk)",
                "Company + [(] + Careof + [)]",
                "","--- Select a Company") %>
```

Creates an HTML listbox. The list can be populated from a data source of a cursor, array or collection and by binding an evaluated expression for text and value displays of each list item and you can add an initial first item.

The first value is the name of the control. The second parameter is the single value that is bound and matches the selected **value** of the control. 

The third, fourth and fifth parameters describe the list data source, and the value and display fields that are displayed when the cursor or collection is iterated.

Instead of a fixed value for binding you can also bind to `Request.FormOrValue()` which uses the value the form value first (in a POST operation) and the supplied value next:

```html
<%= HtmlListBox("lstCustomers",
                [Request.FormOrValue("lstCustomers",poModel.ItemPk)],
                "TT_Cust","TRANSFORM(ttCust.pk)","Company + [(] + ttCust.Careof + [)]",
                "","--- Select a Company") %>
```