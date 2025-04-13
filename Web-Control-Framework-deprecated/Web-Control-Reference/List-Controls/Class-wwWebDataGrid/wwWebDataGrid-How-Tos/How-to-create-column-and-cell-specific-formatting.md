The DataGrid allows you to create custom formatting for each column and with a little bit of extra work each individual cell of the grid. The key to this functionality is the innocuous Format property which allows you to format text.

### Column based Formatting
Applying column based formatting is as easy as applying either a FoxPro format expression. For example, you can use:

@!   - all upper case string
@YL – display a spelled out date format

You can also apply format expressions which are functions that can be called. Functions must follow some simple rules:
<ol>
* Take a single parameter of the value
* Return string output as a response
</ol>
You can call format expressions like this:

=UPPER
=Proper
=MyUdf
=this.Page.FormatField

Each of these functions must take a single input parameter and return a string. A format expression applied in either of these ways formats a column as a whole. 

The most common scenario is probably the last where you call a method of the form to perform your custom formatting. For example:

```foxpro
FUNCTION FormatField(ldDate)
RETURN TimeToC(ldDate) + " PST"
```

### Cell based formatting
You can also create cell based formatting that displays custom formats based on values of a cell. In order to do this you need to create custom expressions. For example, consider a Date column where you want to display all dates after a certain date in a different color than all other dates. To do this you might create a column like this:

```html
<ww:wwWebDataGridColumn ID="colUpdated" runat="server" 
     Expression="this.Page.EnteredExpression(Entered)" 
	HeaderText="Last Update" 
     ItemAttributeString="style='width:130px;background:teal;'">
</ww:wwWebDataGridColumn>
```

You can then implement an EnteredExpression() method on the Page that handles the updating of the value:

```foxpro
FUNCTION EnteredExpression(ldValue)

* ** Highlight filtered entries
IF ldValue > {01/01/2007}
   this.dgDevelopers.ActiveColumnAttributeString="style='background:red;'"
ENDIF

RETURN timetoc(ldValue)
```

The grid's ActiveColumnAttributeString allows you to apply any attributes to the table column. Here I apply a custom style, but you can use a CSS class or any other attribute you see fit - basically whatever you specify gets added to the column definition.

You can also get access to the underlying column object (or any other column):

```foxpro
FUNCTION EnteredExpression(ldValue)

loCol = THIS.gdDevelopers.ActiveColumn    
loCustCol = this.gdDevelopers.Columns.Item("colCompany")

IF ldValue > {01/01/2004}
   loCol.ItemAttributeString="style='background:red;'"
   loCustCol.ItemAttributeString = "style=font-weight: bold;"
ELSE
   * ** IMPORTANT - changing properties on the column 
   * **             changes it for all rows following!	
   loCol.ItemAttributeString="style='background:teal;'"
   loCustCol.ItemAttributeString = ""
ENDIF

RETURN timetoc(ldValue)
```

Generally it's preferrable to  make visual changes using ActiveItemAttributeString since the change is applied only to the current column with any other columns not overridden falling back to the default rendering.  If you modify the column explicitly you have to make sure you reset the column to its default rendering manually (ie. duplicate whatever attributes you have applied in the markup or column definition).