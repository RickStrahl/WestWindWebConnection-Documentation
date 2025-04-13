Web Connection supports the concept of binding errors when using the Databind() and Unbind() methods of the Page class. These methods cause a set of BindingErrors to be set on the page's BindingErrors collection.

Two classes control binding errors:

<ul>
* BindingError
* BindingErrorCollection
</ul>

You'll be interacting mostly with the BindingError collection as a whole most likely as in code as the following:

```foxpro
FUNCTION btnSubmitForm_Click()

this.UnbindData()

IF this.BindingErrors.Count > 0
   * ** Display on the Error Display control
   this.ErrorDisplay.ShowError(this.BindingErrors.ToHtml(),"Please correct the following")
   RETURN
ENDIF

* ** Validate Business Rules
IF !THIS.oDeveloper.Validate()
   this.AddValidationErrorsToBindingErrors(THIS.oDeveloper.oValidationErrors)
   this.ErrorDisplay.ShowError(this.BindingErrors.ToHtml(),"Please correct the following")
   RETURN
ENDIF

* ** Finally save
IF !THIS.oDeveloper.Save()
   this.ErrorDisplay.ShowError("Save Error: " + THIS.oDeveloper.cErrorMsg
ELSE
   this.ErrorDisplay.ShowMessage( "Entry Saved" )
ENDIF    

ENDFUNC
```