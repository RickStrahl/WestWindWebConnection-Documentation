The DataSource that is bound to this control. Can be a Cursor, Array of objects or a collection of Objects.

The ItemTemplate sees each DataItem as a *DataItem* object variable when Array or Cursor data is used.

```html
<%= DataItem.Company %>
```

Cursors can simply use the Cursor.Fieldname or just FieldName syntax:

```html
<%= TQuery.Company %>
<%= Company %>
```