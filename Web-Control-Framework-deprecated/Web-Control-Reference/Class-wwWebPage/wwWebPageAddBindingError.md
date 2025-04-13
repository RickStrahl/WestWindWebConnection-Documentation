Adds a binding error to the page's BindingErrors collection.

This method is called internally for any unbinding errors that occur, but you can also manually add errors to this collection. For example, say you're doing a post check for an 'empty' value on a list selection where the first item is * ** Please select one. In this case you might do:

```foxpro
* ** Unbind all controls on the form
this.UnbindData()

IF this.lstStates.selectedValue = "* **"
	THIS.AddBindingError("You have to select a state","lstStates")
ENDIF

IF this.BindingErrors.Count > 0
	this.ErrorDisplay.Text = this.BindingErrors.ToString()
	RETURN
ENDIF
```