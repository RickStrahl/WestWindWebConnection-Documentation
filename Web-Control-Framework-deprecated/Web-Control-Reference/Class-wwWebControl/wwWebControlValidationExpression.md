Validation Expression that validates the value that as been unbound by the Unbind() method by Control Source Binding.

You can use any FoxPro expression that evaluates to .T. or .F. The expression executes in the scope of the control so THIS references the control.

.T. indicates that validation is successful. .F. indicates that the validation failed. If failed the message in BindingErrorMessage is set with a generic error message or if the message is already set that value is displayed. This means you can pass the control and set the binding message explicitly. 

**Examples:**  
The simplest thing to do is to use a standard expression like this:

```foxpro
!EMPTY(this.Text)
```

This will display a generic error message (Input is invalid). Alternately you can point at some user defined code of your own to perform the validation. Typically you'll want to pass the control as a reference. For example:

```foxpro
this.Page.ValidateName(THIS)
```

You'd then implement a method on your Web Page clas that does something like this:

```foxpro
FUNCTION ValidateName(loControl)

IF loControl.Text != "Some Value"
   loControl.BindingErrorMessage = "Some Value must be entered."
   RETURN .F.
ENDIF

RETURN .T.
```

To take this one step further you might create a more generic validation handler for the Page:

```foxpro
THIS.Page.ControlValidation(this)
```

Then implement a method for this functionality on your Web Page:

```foxpro
FUNCTION ControlValidation(loControl)
DO CASE
CASE loControl.Id = "txtValue"
   IF loControl.Text $ "Value1,Value2,Value3"
      * ** To assign a customer validation error message assign here:
      loControl.BindingErrorMessage = "Make sure the value is correct."
      RETURN .F.
   ENDIF
CASE loControl.Id = "txtValue2"
   ...
ENDCASE

RETURN .T.
```

Note that you can create a custom validation error message by assigning the BindingErrorMessage to the control. This gives you full control for the message displayed in the summary and next to the control.