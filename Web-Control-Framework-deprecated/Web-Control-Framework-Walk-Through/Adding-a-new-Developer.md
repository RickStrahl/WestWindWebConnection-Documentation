In the last step we allowed editing existing developers. Now let's add the ability to add new developers. This is pretty simple as you are going to use the same user interface.

The key difference in this process is that we don't have a new Id for the developer until we save. This presents an interesting problem the way the app works currently as it won't go passed a missing Id. So what needs to happen is we need to allow entries through when there's no Id and create a new or empty developer record we can fill data into. We can then go ahead enter data and save the entry, at which point a real ID will be assigned.

To make this work we need to keep track of this new ID. One way to do this is to store in ViewState, which is essentially page specific state. In addition to checking for an Id on the querystring we'll also check for it in ViewState. If in neither we find an ID we assume it's a new developer. Here's the code that goes into the Page OnLoad for the EditDeveloper_Page.prg file:

```foxpro
FUNCTION OnLoad()

this.oDeveloper = NEWOBJECT("wwDevRegistry","wwDevRegistry.vcx")

* ** Check for the ID in QueryString and ViewState (new)
this.nId = VAL(Request.QueryString("Id"))
IF this.nID = 0
   * ** Viewstate returns .null. if not present
   this.nId = VAL( this.ViewState.Item("Id") )
   IF ISNULL(this.nId)
      this.nID = 0
   ENDIF
ENDIF   

* ** If it's empty create a new developer (not saved yet)
IF THIS.nId # 0  
   * ** Load existing developer
   IF !this.oDeveloper.Load(this.nId)
      Process.ErrorMsg("Invalid Developer",;
                       "Please make sure you select a valid developer to display.",,;
                       5,"developerlist.wcsx")
      RETURN
   ENDIF
ELSE
   this.oDeveloper.New()
ENDIF      

... more code omitted
ENDFUNC
```

Notice that I changed the lnId variable to a property of the form - we'll need to look at it when we save our entry in the end. This code checks for the Id in both Querystring and ViewState if it doesn't find anything creates a new, empty Developer record which is not yet saved.

You should now be able to run the form and see it display with all empty fields if you go to:

<a href="http://localhost/DevDemo/EditDeveloper.wcsx" target="top">http://localhost/DevDemo/EditDeveloper.wcsx</a>

That's half of it. The other half is actually saving the data and the question is how do you know how you need to save the data for a new record or an existing record? In this case it doesn't really matter, because the business object will figure it out on its own. But if you need to detect the new record you simply check for THIS.nID = 0. So the button click now looks like this:

```foxpro
FUNCTION btnSubmit_Click()

* ** Unbind the data back into the control source for this ID
this.UnbindData()

IF !this.oDeveloper.Validate()
   this.AddValidationErrorsToBindingErrors(;
              this.oDeveloper.oValidationErrors)
ENDIF

* ** Demonstrate manually adding a binding error
IF EMPTY(this.txtState.SelectedValue)
   this.AddBindingError("Please select a state","lstState")
ENDIF

* ** If we have binding errors now we need to display them
* ** and not save the developer entry yet
IF THIS.BindingErrors.Count > 0
   this.ErrorDisplay.Text = this.BindingErrors.ToHtml()   
   RETURN
ENDIF

* ** Approve here so it shows up in the list
THIS.oDeveloper.oData.Approved = .T.

* ** If we get here there are no errors
IF !this.oDeveloper.Save()
   this.ErrorDisplay.Text = this.oDeveloper.cErrorMsg
   RETURN
ENDIF

IF this.nID = 0
   * ** Add the new id into ViewState
   this.ViewState.Add("Id",this.oDeveloper.oData.Pk)

   * ** Rebind image now that the value is set
   this.imgLogo.DataBind()  
ENDIF   

this.ErrorDisplay.ShowMessage("Developer Entry Saved")
ENDFUNC
*  editDeveloper_Page :: btnSubmit_Click
```

Functionally there's only one small block of code that has changed in this method to support adding a new record:

```foxpro
IF this.nID = 0
   * ** Add the new id into ViewState
   this.ViewState.Add("Id",this.oDeveloper.oData.Pk)
ENDIF
```

This code makes sure that the new Id gets written into Viewstate, so that after we save and we come badk to redisplay the page, the the OnLoad can now find the new ID for this new developer in the ViewState and load up the data properly.

### A few words about ViewState
Viewstate is very handy to persist values for a specific page. You might have used Session state or hidden form fields for this stuff before, but now you can simply stick any values you need to see on a postback into the ViewState and have access to them on the next hit. Note that ViewState values are limited to values that convert easily into strings and back with the TRANSFORM() function. All values are returned as strings.


And there you have it. We've done the whole cycle - view data, drill into it to view and edit it, and then finally add to it.

Next: A few more additions