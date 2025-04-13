Creates an HTML Drop Down list. The DropDown can be populated from a data source of a cursor, array or collection and by binding an evaluated expression for text and value displays of each list item and you can add an initial first item.

```html
<%= HtmlDropDown("lstCustomers",
                TRANSFORM(poModel.Pk),
                "TT_Cust","TRANSFORM(ttCust.pk)","Company + [(] + ttCust.Careof + [)]",
                "","--- Select a Company") %>
```

The first value is the name of the control. The second parameter is the single value that is bound and matches the selected **value** of the control. 

The third, fourth and fifth parameters describe the list data source, and the value and display fields that are displayed when the cursor or collection is iterated.

Instead of a fixed value for binding you can also bind to `Request.FormOrValue()` which uses the value the form value first (in a POST operation) and the supplied value next:

```html
<%= HtmlDropDown("lstCustomers",
                [Request.FormOrValue("lstCustomers",poModel.ItemPk)],
                "TT_Cust","TRANSFORM(ttCust.pk)","Company + [(] + ttCust.Careof + [)]",
                "","--- Select a Company") %>
```