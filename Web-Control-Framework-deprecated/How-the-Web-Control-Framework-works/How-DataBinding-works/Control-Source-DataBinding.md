ControlSource Databinding allows you to bind all the controls on a form that have a ControlSource set to a property of an object, a variable or database field. Essentially it works just like ControlSource binding in a Desktop form, except that that binding has to be done explicitly by calling the DataBind() and UnbindData() methods of the Page object or each control.

The most common scenario looks like this:
<ol>
* Create your controls and set their ControlSource
* On the first page hit (!this.IsPostback) you DataBind() the control sources in OnLoad()
* Subsequent hits are not re-bound, except for non-PostBack fields
* You save on a button click or other action
* In the save operation you reload the original data, then call Page.UnbindData() to unload the ControlSources
</ol>

If we put this into code imagine a simple page that binds to a customer object from a table. For clarity, I'll use a cursor with SCATTER NAME commands here to create objects to bind to, but realize that you should probably use a business object and bind to its properties. 

Assume that in this page there are a number of controls that have their ControlSource set this.oCustomer.Company, this.oCustomer.Name etc. for binding.

```foxpro
DEFINE CLASS Customer_Page as wwWebPage

oCustomer = null
nPk = 0

* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
* CustomerPage ::  Onload
* *** *** *** *** *** *** *** *** *** *** *** *** ***
* **  Function: Onload method of the form fires on EVERY hit to the 
* **			   page. Note that you don't want to bind every time,
* **			   only on the first hit
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
FUNCTION Onload()

* ** Something to select the customer we'll bind to
this.nPk = VAL(Request.QueryString("ID"))

* ** Only bind on the first page hit
* ** On Postbacks we want to preserve the values entered
IF !this.IsPostBack
	SELECT * from wwDevRegistry WHERE PK = this.nPK ;
	     INTO CURSOR TQuery
	IF _TALLY < 1
		this.ErrorDisplay.Text = "Invalid Customer"
		RETURN
	ENDIF
	SCATTER NAME this.oCustomer MEMO
	THIS.DataBind()    && Bind the control to this.oCustomer fields     
ENDIF

* ** Any non-postback controls like labels, images etc. need to be manually rebound
this.lblMessage.DataBind()

ENDFUNC

* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
* CustomerPage ::  btnSave_Click
* *** *** *** *** *** *** *** *** *** *** *** *** ***
* **  Function: Method that saves the customer entry. We have to reload
* ** 		   the data first so that we have a base object (or record)
* **			   that the page can unbind to.
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
FUNCTION btnSave_Click()

* ** Must reload the data first so we have something to bind back to
IF !USED("wwDevRegistry")
   USE wwDevRegistry IN 0
ENDIF
SELE wwDevRegistry

LOCATE FOR Pk = this.nPk
IF !FOUND()
	THIS.ErrorDisplay.ShowError("Invalid Record...")
	RETURN
ENDIF
SCATTER NAME this.oCustomer MEMO

this.UnbindData()

* ** Check for binding errors and display on Error control
IF this.BindingErrors.Count > 0
	this.ErrorDisplay.Text = this.BindingErrors.ToHtml()
	RETURN
ENDIF	

ENDFUNC

ENDDEFINE
```

The key thing to understand about ControlSource Binding is that you need to typically bind only once in the lifetime of the form on the Initial page hit which is an HTTP GET. The page is accessed only and displayed. You don't want to rebind the page on subsequent hits because binding overwrites the values the user might have entered into the form.

To avoid rebinding on every hit you'll want to check Page.IsPostBack which determines whether the page is in a GET or POST operation - POST operations occur only when you're clicking a button or take another action.

When you want to save the data, you need to make sure that you have something to bind back to. IOW, you have to create the object again preferrably with the original data you used to load the form. This object is then used to receive the values that the user has entered on the HTML form when you call UnbindData().

In almost any scenario you'll want to check for binding errors after UnbindData() is called by checking the BindingErrors collection. BindingErrors occur if a value is unbound and cannot be stored back into the underlying field. This can include things like invalid number or date formats or because of validation rules you've set on the control (like IsRequired). The BindingErrors Collection allows you to easily get a list of errors and display them on a form in a rich environment. The BindingErrors collection works together with the controls on the form and the Page to provide a rich error display to the user.

![](IMAGES%5CWebControls%5CEditDeveloper_WithErrors.png)