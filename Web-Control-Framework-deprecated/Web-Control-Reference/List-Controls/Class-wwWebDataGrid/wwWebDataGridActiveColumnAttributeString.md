The content of the column's ItemAttributeString that is used to render the current column. This value can be safely overridden in any databinding (ControlSource) or formatting expression to affect only the active column.

Note that this is the preferred mechanism for changing rendering of individual columns conditionally. Say if you want to highlight specifc dates in a data column you might code like the following:

```foxpro
FUNCTION EnteredColumnExpression(ltEntered)

IF ltEntered >= {^2007/01/01}
	this.dgCustomers.ActiveColumnAttributeString = "style='background: green;'"
ENDIF

RETURN ShortDate(ltEntered,2)
ENDFUNC
*   EnteredColumnExpression
```

Unlike modifying the ActiveColumn directly - which affects all remaining rows rendered with this column definition - changing the ActiveColumnAttributeString is applied only to the current column, leaving the original column ItemAttributestring intact.