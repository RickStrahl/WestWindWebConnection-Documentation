Header text for the column that is displayed in the header row.

The header text can be specified as a static string or - if prefixed with an = - an expression. For example, if you wanted to display the current date time in the header you'd use:

```foxpro
loCol.HeaderText = "=DATETIME()"
```