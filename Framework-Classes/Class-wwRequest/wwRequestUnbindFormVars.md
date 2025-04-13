Unbinds the `Request.Form` object into a passed in FoxPro object, by mapping the properties of the object to form variable names. The method then uses `Request.Form(field-name)` to retrieve the value and stores it in each property of the object.

In order for this to work the form variables of **the form have to match the property names of the object**, or match with a prefix. Objects are bound at a single level only - there's no hierarchical binding although you can use multiple calls to this method to bind each level of an object.

### Binding Errors
This method **returns a collection of Binding Errors** that contain an error message and the name of the variable bound.

```foxpro
loErrors = Request.UnbindFormVars(poCustomer)
IF (loErrors.Count > 0)
  pcErrorMsg = "There are binding errors." + ;
               loErrors.ToHtml()
ENDIF
```

Alternately you can iterate through the list:

```foxpro
loErrors = Request.UnbindFormVars(poCustomer)
IF (loErrors.Count > 0)
  FOR lnX = 1 to loErrors.Count
      loError = loErrors.Item(lnX)
      
      *** Handle number and date fields explicitly so we can append a useful name
      IF loError.cObjectName  = "billrate"
         loError.cMessage = loError.cMessage + " for Bill Rate."
      ENDIF
      IF loError.cObjectName = "entered"
         loError.cMessage = loError.cMessage + " for Entered."
      ENDIF
  ENDFOR
ENDIF
```