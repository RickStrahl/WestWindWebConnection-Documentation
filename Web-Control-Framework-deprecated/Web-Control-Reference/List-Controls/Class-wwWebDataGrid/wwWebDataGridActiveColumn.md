The active grid column object instance during databinding. This property can be used to get access to the individual column that is current active for databinding.

This property is merely a short cut for code like this: 

```foxpro
loCol = this.gdData.Column.Item("activeColumn")
```

Note that if you change any properties on this column you are changing the core column definition and the column will render with your changes for any remaining data rows.

If you need to modify only display attributes or styles and do so for individual rows, you may use ActiveColumnAttributeString which is restored for each column rendering.