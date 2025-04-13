Allows you to a wwBusiness ValidationErrors collection and automatically add these errors to the BindingErrors Collection. 

Makes it real easy to display error information from your business objects. 

```foxpro
IF !this.oCustomer.Validate()	
	THIS.AddValidationErrorsToBindingErrors(this.oCustomer.oValidationErrors)
ENDIF

IF this.BindingErrors.Count > 0
	this.ErrorDisplay.Text = this.BindingErrors.ToString()
	RETURN
ENDIF
```