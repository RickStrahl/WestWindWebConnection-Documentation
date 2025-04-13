The wwHtmlHelpers library contains a set of HTML helper functions that allow easy creation of HTML elements and form controls with a single line of code. 

Helpers are useful for use in raw FoxPro code in Process class methods where you can create HTML and output it like this:

```foxpro
*** Create a textbox
Response.Write( HtmlTextBox("txtLastName",loCustomer.oData.LastName) )
```

You can also easily use Html Helpers in Templates or Scripts:

```html
< %= HtmlTextBox("txtLastName",loCustomer.oData.LastName) % >
```

which makes these helper functions nice and quick ways to embed more complex HTML into the page. 

Helpers are also great to use in Web Control Framework (or the HtmlDataGrid) binding expressions. For example imagine you want to embed a hyperlink into a column of a wwWebDataGrid - you can do this by binding HtmlLink() to a column expression like this:

```foxpro
loColumn.Expression = [HtmlLink("customer.wwd?id=" + TRANS(cust.pk),cust.company)]
```

### Automatic PostBack on Input Controls
The various input controls like HtmlTextBox, HtmlCheckBox, HtmlListBox/HtmlDropDown etc. all automatically pick up their values from the Web Connection Request.Form() object on a POST operation by default. This means that you don't have to explicitly assign a value to the control on a POSTBACK operation - the control will automatically display the last assigned or altered value from the user from the Request.Form() buffer.

If you bind like this:
```foxpro
lcHtml = HtmlTextBox("txtCompany",loCustomer.oData.Company)]
```

The value first checks for Request.Form("txtCompany") and if found assigns that value rather than the value of loCustomer.oData.Company. In most cases this is the desired behavior since first display will render the value from the object, and subsequent displays of the page will render the value from the Request.Form() buffer.

To override AUTOPOSTBACK behavior you can append ":FORCED" to the end of the value. The function will strip the ":FORCED" postfix of the resulting string 
to set the value of the control:

```foxpro
lcHtml = HtmlTextBox("txtCompany",loCustomer.oData.Company + ":FORCED")]
```

This causes the loCustomer.oData.Company value to always be assigned every time the expression is run - no retrieval of Request.Form("txtCompany") occurs. The ":FORCED" is simply removed from the result string (ie. "West Wind Technologies:FORCED" is turned into "West Wind Technologies" only).