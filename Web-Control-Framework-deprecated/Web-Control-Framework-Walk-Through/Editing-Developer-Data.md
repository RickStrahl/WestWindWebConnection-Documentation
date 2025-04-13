In the last sample we displayed data. Now let's allow editing of this data.

Create a new page and call it EditDeveloper.wcsx add a wwWebPage control and assign the class names as shown below:

```html
<%@ Page Language="C#" 
	ID="EditDeveloper_page" 
	GeneratedSourceFile="DevDemo\EditDeveloper_page.prg"
%>
<%@ Register Assembly="WebConnectionWebControls" 
	Namespace="Westwind.WebConnection.WebControls"
	TagPrefix="ww" %>
<html >
<head runat="server">
    <title>Developer Editor</title>
    <link href="westwind.css" rel="stylesheet" type="text/css" />
</head>
<body>
 <form id="form1" runat="server">

  </form>
</body>
</html>
```

So now, we already did the base layout in the last topic, so let's simply copy the content from between the <FORM> tag from the ShowDeveloper.wcsx page and paste it into this page. The go in and do a Search and Replace for wwWebLabel to wwWebTextBox. And voila right of the top we've adjusted most of the controls and are ready for editing. A bit more work will be required for the Email and Web Link fields, but for now this is a good start.

Go ahead and run the form, incomplete as it from the browser:

<a href="http://localhost/DevDemo/editdeveloper.wcsx?id=3" target="top">http://localhost/DevDemo/editdeveloper.wcsx?id=3</a>

Looks a bit haphazard, the textboxes are empty and there are some script errors on the page, but it's not too bad for a start. Let's fix a couple visual things. Drop a westwind.css stylesheet onto the page, and change the width of the all the textboxes to 350. You can do this by selecting all the controls in the VS.NET editor and applying the width.

There are a couple of more things that you should fix here:

* Do a search and replace on lbl and replace with txt (will help with error info)
* Check the Address (txtAddress) field and remove the DisplayMemo() function around the field

The errors we're seeing on the running page are because we haven't hooked up any code yet to load the actual developer business object. So let's create our form's OnLoad method. This code should be identical to what we were doing in ShowDeveloper_page to start with:

```foxpro
**************************************************************
DEFINE CLASS EditDeveloper_page as WWC_WEBPAGE
***************************************
*** Your Implementation Page Class - put your code here
*** This class acts as base class to the generated page below
**************************************************************

oDeveloper = null
oLookups = null

FUNCTION OnLoad()

this.oDeveloper = NEWOBJECT("wwDevRegistry","wwDevRegistry.vcx")

lnId = VAL(Request.QueryString("Id"))
IF lnId = 0 OR !this.oDeveloper.Load(lnId)
   Process.ErrorMsg("Invalid Developer",;
                    "Please make sure you select a valid developer to display.",,;
                    5,"developerlist.wcsx")
   RETURN
ENDIF

this.oLookups = NEWOBJECT("wwLookups","wwDevRegistry")
lnResult = this.oLookups.GetCountries()

this.lstCountry.DataSource = "TCountries"
this.lstCountry.DataTextField = "Country"


IF !this.IsPostBack
   THIS.DataBind()
ENDIF   

*** Must always databind the image since it doesn't post back
this.imgLogo.DataBind()

ENDFUNC

ENDDEFINE
```
The only difference here is that I changed the DataBind to only fire when we are not posting back. This makes sure that we don't reload the data after we've made changes, but before the data has saved, such as when errors occur during validation. Note that I manually bind the Image however - the image doesn't post its value back, so we want to rebind this value on every hit. Again note the image control will automatically not render if the ImageUrl isn't set.

So we now have a functioning page that displays the developer information in a mostly editable environment. The page looks like this now:

![](IMAGES\WebControls\EditDeveloper_1.png)

Not bad considering we've basically picked up most of this from the previous page. We need to do more work though. We'll need to fix the links and make them editable as well as the Services description on the bottom. So let's start with the links. The links should merely convert to textboxes as well. So let's do that:

```html
<ww:wwWebTextBox ID="txtEmail" runat="server"  
           ControlSource="this.Page.oDeveloper.oData.Email" Width="350" />

<ww:wwWebTextBox ID="txtWebSite"  runat="server"  
            ControlSource="this.Page.oDeveloper.oData.WebSite" Width="350"  
            ondblclick="window.open(this.value,'DevSite');" style="color:blue" />
```

For the Services area below we can use the following expression:

```html
<ww:wwWebTextBox runat="server" ID="txtServices  
                ControlSource="this.Page.oDeveloper.oData.Services" 
                TextMode="MultiLine" width="400" Height="350" />
```


### Adding a Country Lookup
Let's make one more change to show you how easy it is to create a drop down list and have it databind. Let's change the Country field to a dropdown list based on the wwLookups::GetCountries() business object method. This method returns a cursor of country names we can databind into a dropdown list. 

Let's start by replacing the txtCountry field with a lstCountry field in the markup like this:

```html
<ww:wwWebDropDownList ID="lstCountry" runat="server"  Width="350px"
                      ControlSource="this.Page.oDeveloper.oData.Country" >
</ww:wwWebDropDownList>
```

This tells the control to bind to the Country field of our business object. The binding here is for the selected value of the control. In order to bind the list of countries we need to use a little code in the code behind page in the OnLoad() of the Page class:

```foxpro
this.oLookups = NEWOBJECT("wwLookups","wwDevRegistry")
lnResult = this.oLookups.GetCountries()

this.lstCountry.DataSource = "TCountries"
this.lstCountry.DataTextField = "Country"
```

In this case I'm only binding the text field, but I can also bind a DataValueField if necesary. And that's all it takes.

So now our form looks like this:

![](IMAGES\WebControls\ShowDevelolper_3.png)

Since we're going to handle errors, we might as well one more thing. Select all the TextBoxes and set the IsRequired Property to true to force the fields to be populated. This will give us Binding errors we can display on the fields if they are blank.

### Saving the Data
Time to start saving the data. So let's add a button to the bottom of the form first:

```html
<ww:wwWebButton ID="btnSubmit" runat="server" AccessKey="s" 
   Text="Save Developer Info" Click="btnSubmit_Click" />
```

In addition let's also add an wwWebErrorDisplay control onto the page so we can display errors in a meaningful way. While you can use a simple label for error display the wwWebErrorDisplayControl does a much nicer job of it. Go back into VS.NET and drag and drop the control onto the form above the image control:

```html
<ww:wwWebErrorDisplay ID="ErrorDisplay" runat="server" CssClass="ErrorDisplay" ErrorImage="images/warning.gif"
         InfoImage="images/info.gif" Text="" UserMessage="Please correct the following:" />
```

If you're using VS.NET your layout now should look like this:

![](IMAGES\WebControls\ShowDevelolper_vsnetview.png)

### Saving User Changes
Ok, let's run the page to make sure everything works so far. The page should now run well in display mode. If you end up clicking the button on the bottom of the form you'll get an error because we haven't implemented the button click event yet. So let's implement it. Here's the code we're going to use:

```foxpro
************************************************************************
* editDeveloper_Page :: btnSubmit_Click
****************************************
***  Function: Save the Developer Information by unbinding 
***            and validating the input.
************************************************************************
FUNCTION btnSubmit_Click()

*** Unbind the data back into the control source for this ID
this.UnbindData()

IF !this.oDeveloper.Validate()
   this.AddValidationErrorsToBindingErrors(this.oDeveloper.oValidationErrors)
ENDIF

IF THIS.BindingErrors.Count > 0
   this.ErrorDisplay.Text = this.BindingErrors.ToHtml()   
   RETURN
ENDIF

*** If we get here there are no errors
IF !this.oDeveloper.Save()
   this.ErrorDisplay.Text = this.oDeveloper.cErrorMsg
   RETURN
ENDIF

this.ErrorDisplay.ShowMessage("Developer Entry Saved")
ENDFUNC
*  editDeveloper_Page :: btnSubmit_Click
```

That's all we need to deal with doing basic error checking and saving the data! Very little code here. The first thing that happens is the data from the form is unbound back into their control sources, which moves all the field values back to the business object oData fields.

The UnbindData() method does the work of looking at the control sources and retrieving the data from each control. If an error occurs during the data conversion, UnbindData() stuffs the errors in the BindingErrors collection. You can retrieve the binding errors directly or you can use the ToString() or ToHtml() methods which return string and HTML presentations of the errors.

Next we call the Validate method of the business object. Validate is basically checking business rules before saving the data. Internally the business object updates an oValidationErrors collection with business rule violations. The code looks something like this.

```foxpro
*** wwDevRegistry::Validate()
LOCAL loDev

loDev = THIS.oData

this.cErrorMsg = ""
this.oValidationerrors.Clear()

IF EMPTY(loDev.Company)
   this.AddValidationerror("A company name is required.","txtCompany")
ENDIF
IF EMPTY(loDev.Name)
   this.AddValidationError("A contact name is required.","txtName")
ENDIF
IF LEN(loDev.Services) < 200
   this.AddValidationError("The service description is too short. At least 200 characters are required.","txtServices")
ENDIF

IF this.oValidationErrors.Count > 0
	THIS.SetError(this.oValidationErrors.ToString())
	RETURN .F.
ENDIF

RETURN .T.
```

Note that this uses a wwBusiness object, and this is really an implementation detail. You can do your validation any way you choose. One nice thing about the wwBusiness oValidationErrors collection is that it can be automatically added to the BindingErrors collection of the WebPage which combines both. The following code does this:

```foxpro
IF !this.oDeveloper.Validate()
   this.AddValidationErrorsToBindingErrors(this.oDeveloper.oValidationErrors)
ENDIF
```

If an error occurs you get a rich error display in the ErrorDisplay control, which provides a nice display for our error information with this code:

```foxpro
IF THIS.BindingErrors.Count > 0
   this.ErrorDisplay.Text = this.BindingErrors.ToHtml()   
   RETURN
ENDIF
```

To check this out go into the form and leave a couple of field blank or leave just a few characters of text in the Services field. You should get a page with rich error info that looks like this:

![](IMAGES\WebControls\EditDeveloper_WithErrors.png)

The first error in the figure is a Binding Error - the IsRequired flag was triggered upon saving. The second is a business rule violation error. Both are displaying in the same error display.

Cool isn't it? Notice that you can click on the links in the error and it will highlight the field and put the cursor there. Also notice the mouseover effect (in IE at least) for the error icons and the ability to specify where you want the error icons placed. BTW, the icon itself is configured with the Page's ErrorIconUrl property - it defaults to the application root's images directory and the warning.gif file. 

If you 'fix' the problems (or simply reset the page), then make a more minor change, you can see what the save operation looks like. Notice it calls the ShowMessage() method of the ErrorDisplay object, which simply displays a message with an info icon. ShowMessage is meant for plain informational messages and provides a diffferent display mode:

```foxpro
this.ErrorDisplay.ShowMessage("Developer Entry Saved")
```

Note that most aspects of this are configurable. The display of the error box is CSS driven, and the messages and images are configurable via control properties as well. This is automatic error handling is very easy to use and requires very little code. Error handling in classic Web Connection applications was often omitted or used really simple pages because it was such a pain to build. Now error handling is so easy that it practically is a no-brainer.

Next: [Adding a new Developer](vfps://Topic/_1MA071D1V)