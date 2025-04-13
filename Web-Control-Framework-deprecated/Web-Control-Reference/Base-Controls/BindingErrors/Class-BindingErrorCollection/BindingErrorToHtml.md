Returns an HTML result string for the active binding errors in the collection. This method generates the basic message as well as javascript links to activate and highlight each of the controls when clicked from the selection list that is generated.

Typically the result from a BindingErrorCollection is stored on Page.BindingErrors and is used to bind to an ErrorDisplay control. The following code might be used in a click button to save data from a form:

```foxpro
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
```

<div class="notebox">**wwWebTabControl Note:**  
If you're using the wwWebTab control and you have controls with errors that are hidden, the default control activation code will not cause the pages to be activated and so clicking the error link will not show the control. You can implement a global JavaScript [OnBindingErrorLink(element) function](vfps://Topic/_2BY0QZYF3) to intercept error link clicks and optionally activate the page explicitly.</div>