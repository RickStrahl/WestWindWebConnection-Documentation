In the last step we completed the editing and adding sequence. In this final step let's add a few more minor details and then summarize and show the final code here.

The first thing you might have noticed is that I glossed over a few more minor feature enhancements I made. I added a State dropdown to the form and databound it to a cursor to display the states. Here's the WCSX control declaration:

```foxpro
<ww:wwWebDropDownList ID="txtState" runat="server" Width="350px"
                              ControlSource="this.Page.oDeveloper.oData.State" >
```

The data for this list comes from the wwd_lookups table and is retrieved via the wwLookups business object, which returns a cursor of two fields: State and StateCode. Unlike the Country drop down used earlier here we want to bind both the StateCode as the value we save and start with and the State name which is displayed in the list:

```foxpro
lnResult = this.oLookups.GetStates()
this.txtState.FirstItemText = "* ** Please enter a State"
this.txtState.DataSource = "TStates"
this.txtState.DataTextField = "State"
this.txtState.DataValueField = "StateCode"
```

Notice that I also added a * ** Please enter a State item to the listbox's bound data which has an empty value. The Country drop down doesn't need this as it defaults to United States on a new entry, but the state value should be indeterminate until you make a selection of a specific state. 

### Manually adding BindingErrors
The databinding and unbinding is automatically handled by the code we already have, however, the error checking for it is not. The business object doesn't handle this (actually it should, but for arguments sake let's say it doesn't <g>). So we need to manage this one piece of validation ourselves in the code. 

What we want to do is check for the empty Selected value and then add an error to the BindingErrors of the page which is as simple as this in the btnSubmit_Click method after UnbindData():

```foxpro
* ** Demonstrate manually adding a binding error
IF EMPTY(this.txtState.SelectedValue)
   this.AddBindingError("Please select a State","txtState")
ENDIF
```

This demonstrates that you can easily add your own binding errors that participate in the rest of the form based validation routines. You probably have to do more of this if you don't use wwBusiness objects or your own objects that implement some way to easily assign validation errors to the BindingErrors automatically.

If you use another business object framework you should be able to create subclass of the wwWebPage class that provides the ability to automatically add the binding errors similar to this code shown earlier:

```foxpro
IF !this.oDeveloper.Validate()
   this.AddValidationErrorsToBindingErrors(;
              this.oDeveloper.oValidationErrors)
ENDIF
```

which makes business object rule violations a snap to display!

### Complete Script and PRG code for the EditDeveloper Form
I've made a lot of changes in small snippets here, so I thought I'd finish up this walkthough by pasting the full WCSX source and the full source of the custom class I created. There are a few minor enhancements over what I previously showed, but it's pretty close to the walkthrough code.

Here's the complete source code for the VFP CodeBehind class:
```foxpro
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ****
DEFINE CLASS EditDeveloper_page as WWC_WEBPAGE
* *** *** *** *** *** *** *** *** *** *** *** *** **
* ** Your Implementation Page Class - put your code here
* ** This class acts as base class to the generated page below
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ****

oDeveloper = null
oLookups = null

nId = 0

* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
* EditDeveloper_Page :: OnLoad
* *** *** *** *** *** *** *** *** *** *** *** *** *** 
* **  Function: Handles initial display of the developer 
* **            and list binding
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
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

* ** Populate the Country Lookups
this.oLookups = NEWOBJECT("wwLookups","wwDevRegistry")
lnResult = this.oLookups.GetCountries()

* ** Data bind the TCountries result cursor to the listbox
this.txtCountry.DataSource = "TCountries"
this.txtCountry.DataTextField = "Country"

lnResult = this.oLookups.GetStates()
this.txtState.FirstItemText = "* ** Please enter a State"
this.txtState.DataSource = "TStates"
this.txtState.DataTextField = "State"
this.txtState.DataValueField = "StateCode"

* ** Bind the input controls only on the first hit
IF !this.IsPostBack
   * ** Bind all the single field controls
   THIS.DataBind()
ELSE
   * ** Must always databind the image since it doesn't postback
   this.imgLogo.DataBind()
ENDIF   

ENDFUNC
*  EditDeveloper_Page :: OnLoad

* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
* editDeveloper_Page :: btnSubmit_Click
* *** *** *** *** *** *** *** *** *** *** *** *** ***
* **  Function: Save the Developer Information by unbinding 
* **            and validating the input.
* **    Assume:
* **      Pass:
* **    Return:
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
FUNCTION btnSubmit_Click()

* ** Unbind the data back into the control source for this ID
this.UnbindData()

IF !this.oDeveloper.Validate()
   this.AddValidationErrorsToBindingErrors(;
              this.oDeveloper.oValidationErrors)
ENDIF

* ** Demonstrate manually adding a binding error
IF EMPTY(this.txtState.SelectedValue)
   this.AddBindingError("Please select a State","txtState")
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

ENDDEFINE
```

<hr>

```html
<%@ Page Language="C#"
    GeneratedSourceFile="DevDemo\EditDeveloper_page.prg"
    ID="EditDeveloper_page" 
    ErrorIconUrl="images/warning.gif"
 %>
<%@ Register Assembly="WebConnectionWebControls" 
			    Namespace="Westwind.WebConnection.WebControls"
    TagPrefix="ww" %>
    <html>
    <head runat="server">
        <title>Developer Editor</title>
        <link href="westwind.css" rel="stylesheet" type="text/css" />
    </head>
    <body>
        <form id="form1" runat="server">
            <div>
                <h1>
                    Developer Information for
                    <%= this.Page.oDeveloper.oData.Company %>
                </h1>
            </div>
            <br />
            <a href="default.htm">Demo Home</a> | <a href="DeveloperList.wcsx">Developer List</a>
            | <a href="EditDeveloper.wcsx">Add Developer</a> | <a href="<%= Request.GetCurrentUrl() %>">
                Reset Page</a><br />
            <br />
            <ww:wwWebErrorDisplay ID="ErrorDisplay" runat="server" CssClass="ErrorDisplay" ErrorImage="images/warning.gif"
                InfoImage="images/info.gif" UserMessage="Please correct the following:" Center="False"
                Width="500px" />
            <br />
            <ww:wwWebImage runat="server" ID="imgLogo" ControlSource="this.Page.oDeveloper.oData.Logo" />
            <table class="blackborder" width="500" cellpadding="6">
                <tr>
                    <td valign="top" align="right" class="blockheader" style="font-weight: bold">
                        Company:
                    </td>
                    <td valign="top" style="width: 453px">
                        <ww:wwWebTextBox ID="txtCompany" runat='server' ControlSource="this.Page.oDeveloper.oData.Company"
                            Style="font-weight: bold" Width="350px" IsRequired="True"></ww:wwWebTextBox>&nbsp;</td>
                </tr>
                <tr>
                    <td valign="top" align="right" class="blockheader" style="font-weight: bold">
                        Name:
                    </td>
                    <td valign="top" style="width: 453px">
                        <ww:wwWebTextBox ID="txtName" runat='server' ControlSource="this.Page.oDeveloper.oData.Name"
                            Width="350px" IsRequired="True"></ww:wwWebTextBox>&nbsp;</td>
                </tr>
                <tr>
                    <td valign="top" align="right" class="blockheader" style="font-weight: bold; height: 28px;">
                        Address:
                    </td>
                    <td valign="top" style="height: 28px; width: 453px;">
                        <ww:wwWebTextBox ID="txtAddress" runat="server" TextMode="MultiLine" ControlSource="this.Page.oDeveloper.oData.address"
                            Width="350px" IsRequired="True"></ww:wwWebTextBox>&nbsp;
                    </td>
                </tr>
                <tr>
                    <td valign="top" align="right" class="blockheader" style="font-weight: bold; height: 28px;">
                        City:</td>
                    <td valign="top" style="height: 28px; width: 453px;">
                        <ww:wwWebTextBox ID="City" runat="server" ControlSource="this.Page.oDeveloper.oData.City"
                            Width="350px" IsRequired="True"></ww:wwWebTextBox>&nbsp;</td>
                </tr>
                <tr>
                    <td valign="top" align="right" class="blockheader" style="font-weight: bold">
                        State:</td>
                    <td valign="top" style="width: 453px">
                        <ww:wwWebDropDownList ID="txtState" runat="server" Width="350px" ControlSource="this.Page.oDeveloper.oData.State">
                        </ww:wwWebDropDownList></td>
                </tr>
                <tr>
                    <td valign="top" align="right" class="blockheader" style="font-weight: bold">
                        Country:
                    </td>
                    <td valign="top" style="width: 453px">
                        <ww:wwWebDropDownList ID="txtCountry" runat="server" Width="350px" ControlSource="this.Page.oDeveloper.oData.Country">
                        </ww:wwWebDropDownList></td>
                </tr>
                <tr>
                    <td valign="top" align="right" class="blockheader" style="font-weight: bold">
                        Phone:
                    </td>
                    <td valign="top" style="width: 453px">
                        <ww:wwWebTextBox ID="txtPhoneNumber" runat='server' ControlSource="this.Page.oDeveloper.oData.Phone"
                            Width="350px" IsRequired="True"></ww:wwWebTextBox>&nbsp;</td>
                </tr>
                <tr>
                    <td valign="top" align="right" class="blockheader" style="font-weight: bold; height: 28px;">
                        Email:
                    </td>
                    <td valign="top" style="height: 28px; width: 453px;">
                        <ww:wwWebTextBox ID="txtEmail" runat="server" ControlSource="this.Page.oDeveloper.oData.Email"
                            Width="350" IsRequired="True" />&nbsp;</td>
                </tr>
                <tr>
                    <td valign="top" align="right" class="blockheader" style="font-weight: bold">
                        Web Site:
                    </td>
                    <td valign="top" style="width: 453px">
                        <ww:wwWebTextBox ID="txtWebSite" runat="server" ControlSource="this.Page.oDeveloper.oData.WebSite"
                            Width="350" ondblclick="window.open(this.value,'DevSite')" Style="color: blue"
                            IsRequired="False" />
                </tr>
                <tr>
                    <td valign="top" align="right" class="blockheader" style="font-weight: bold">
                        Logo Url:
                    </td>
                    <td valign="top" style="width: 453px">
                        <ww:wwWebTextBox ID="txtLogo" runat="server" ControlSource="this.Page.oDeveloper.oData.Logo"
                            Width="350" ondblclick="window.open(this.value,'DevSite')" Style="color: blue"
                            IsRequired="False" />
                </tr>
                <tr>
                    <td width="131" valign="top" class="blockheader" align="right" style="font-weight: bold">
                        Services offered:</td>
                    <td valign="top" style="width: 453px">
                        <ww:wwWebCheckBox ID="chkDevelopment" runat='server' ControlSource="this.Page.oDeveloper.oData.Dev"
                            Text="Development" />
                        <ww:wwWebCheckBox ID="chkTraining" runat='server' ControlSource="this.Page.oDeveloper.oData.Training"
                            Text="Training" />
                        <ww:wwWebCheckBox ID="chkSupport" runat='server' ControlSource="this.Page.oDeveloper.oData.Support"
                            Text="Support" />
                        <hr>
                        <ww:wwWebTextBox runat="server" ID="txtServices" ControlSource="this.Page.oDeveloper.oData.Services"
                            TextMode="MultiLine" Width="400" Height="150" ErrorMessageLocation="2" />
                    </td>
                </tr>
                <tr>
                    <td align="right" class="blockheader" style="font-weight: bold" valign="top" width="131">
                    </td>
                    <td style="width: 453px" valign="top">
                        <ww:wwWebButton ID="btnSubmit" runat="server" AccessKey="s" Text="Save Developer Info"
                            Click="btnSubmit_Click" /></td>
                </tr>
            </table>
            <br />
            &nbsp;
        </form>
    </body>
    </html>
```