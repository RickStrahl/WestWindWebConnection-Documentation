A common question is how to use Web Controls inside of a Repeater control. A repeater control is basically a template that is repeated over and over and while you can use controls inside of the repeater to bind data to the HTML, unbiding does not automatically work.

The reason for this is that the repeater doesn't call unbind() to unbind itself or child controls. And with good reason - the repeater itself has no idea how to bind back to the data that you've bound it to. The data may have come from anywhere, the data may have changed and so unbinding is not something that Web Connection can handle automatically without risking of screwing up your data. Effectively the wwWebRepeater control is read-only.

### How do I capture Form Data Embedded in Repeater
There are workarounds however, but they're not automated as part of the Web Control framework. Let's take a common example and dissect it: Embedding checkboxes into a Repeater control.

Assume for a second we have a cursor with a Selected, Name and ID property and we want to select unselect the values in this cursor based on the user clicking on the checkboxes. 

Create your repeater and a button like this:

```html
<table class="blackborder">
        <ww:wwWebRepeater ID="repCustomers" runat="server">
            <ItemTemplate>
                    <tr>
                        <td style="width: 32px;">
                            <ww:wwWebCheckBox runat="server" ID="chkSelected" />
                            <HtmlCheckBox("chkSelected_" + TRANS(ID),"",MyCursor.Selected) >                           
                        </td>
                        <td style="width: 250px;">
                            <%= MyCursor.Name %>
                        </td>
                    </tr>
            </ItemTemplate>
        </ww:wwWebRepeater>
        </table>

        <ww:wwWebButton runat="server" ID="btnSave" Text="Save" Click="btnSave_Click" />
```

I'm using the new HtmlCheckBox() Helper function in Web Connection 5.56 and later - you could also manually create the checkbox HTML. Note that the checkbox is created with a unique ID that includes some sort of ID/PK that points back to the table. The HTML created for each checkbox looks like this:

```html
<input id="chkSelected_4" type="checkbox" name="chkSelected_4" />
```

which includes the record ID after the name. 

In the button click you then capture all form variables that match the chkBox:

```foxpro
FUNCTION btnSave_Click()
LOCAL lnCount, lnX

* ** Grab all the checkbox form variables 
DIMENSION laVars[1,2]
lnCount = Request.aFormVars(@laVars,"chkSelected_")

SELECT MyCursor

* ** Because we're dealing with checkboxes which don't post values back if not set
* ** we have to update all values to unselected first. 
* ** If you're running against a full table you'd have to filter this update to match
* ** your subset
UPDATE MyCursor SET Selected = .F.  && where customerId = lnCustId  

FOR lnX = 1 TO lnCount
   * ** Grab the id from the Field ID
   lnId = VAL(STRTRAN(laVars[lnX,1],"chkSelected_"))
   
   UPDATE MyCursor WHERE id = lnId SET Selected = .T.   
ENDFOR

ENDFUNC
*   btnSave_Click
```

Basically you capture all formvars that match the chkSelected_ prefix. Unfortunatly this only selects those checkboxes that are checked (a major shortcoming in HTML IMHO). So we can't just loop through all the formvars but have to actually clear out all selections first. Here I'm UPDATEing all records and set them to .F. but most likely you'd have a filter to apply that update to only update the items for a given customer (or whatever) or other filter group.

Once that's done we'll loop through the checkbox form vars captured and assign each one in turn using an UPDATE statement to the database.

It's a little easier if you do this with plain text fields as you CAN loop through all the formvariables to assign the values - Checkboxes and Radiobuttons are the ones that require special attention and basically setting all values to unselected first before assigning selections to it.

### Other Options
Yup it's a bit messy but unfortunately necessary when posting back data from 'lists'. 

It's often better to leave list read only and drill into data screens that allow data updates of a single record explicitly. If you're updating more than one field this is definitely the cleaner way to go.

Another option is to use AJAX. Especially for things like a checkbox you can use a simple callback to the server to update a value in the database immediately without the page requiring a full Postback. 

For the above example you can add a wwAjaxMethodCallback control to the page and call back to the page to update the checkbox. Here's the client HTML and script changes:

```javascript
<ww:wwAjaxMethodCallback runat="server" id="Proxy" />


    <script type="text/javascript">
        $(document).ready(function () {

            // select checkboxes: .blackborder class and any checkbox elements below
            $(".blackborder input[type='checkbox']")
                .click(function () {
                    // strip out the id from the name
                    var id = this.id.replace("chkSelected_", "") * 1;

                    // figure out the status of the checked property on the checkbox
                    var status = $(this).prop("checked") ? true : false;

                    // Make AJAX call and pass two parameters and call the function below
                    // with the result from the callback
                    Proxy.callMethod("UpdateCheckBox", [status, id * 1],
                       function (resultMessage) {
                           showStatus(resultMessage);
                       });
                });
        });
    </script>
```

This code basically selects all the checkboxes below the blackborder CSS class in the page when the page loads and hooks up a Click handler. When clicked we retrieve the clicked status and the ID of the checkbox (this), and then make an AJAX callback to the server. The callback calls back to the same page and a method in that page.

Then on the server side add a method call UpdateCheckbox which looks like this:

```foxpro
FUNCTION UpdateCheckBox(llValue,lnId)

IF VARTYPE(lnId) = "N"
    UPDATE MyCursor WHERE Id = lnId SET Selected = llValue
    RETURN "Checkbox updated successfully: " + TRANSFORM(lnId)
ENDIF

RETURN "Unable to update selected status on item : " + TRANSFORM(lnId)
ENDFUNC
*   UpdateCheckBox
It's pretty straight forward actually
```

The same approach can be used with single textbox where you could hook the *change* event for example.