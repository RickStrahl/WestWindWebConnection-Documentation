The wwModalDialog control allows you to create  popup dialog window that is displayed ontop of all other content. It overlays all existing content with a semi transparent overlay that indicates that the content underneath is not active and accessible effectively giving the appearance of a modal dialog.

![](IMAGES%5CWebControls%5CControls%5CwwWebModalDialog.png)

In this example the modal dialog is the identification box, and you can see the background of the underlying page being dimmed. By default the background is an opaqued percentage of black - in this example a custom <div> tag is used to provide a custom background that is displayed which contains a background image and then displayed at a .89 opacity.

wwModalDialog inherits from Panel so you the basic unit that you work with in the designer is basically a panel control which you can fill with any content that you choose. A typical layout however contains a header and a content panel and looks something like this:

```html
<ww:wwModalDialog ID="MessageBox2" runat="server" 
    ContentId="MessageBox2Content" HeaderId="MessageBox2Header" 
    overlayId="overlay" BackgroundOpacity=".85"
    OkButtonId="MessageBox2Ok" CancelButtonId="MessageBox2Cancel" 
    OnClientDialogClick="OnMessageBox2Click"    
    DragHandleId="MessageBox2Header" Draggable="true"            
    Style="background: white; display:none;" cssclass="blackborder"
    Width="400px"
>
    <div id='MessageBox2Header' class='gridheader'>Identification</div>
    <div style='padding: 10px;'>
        <div id='MessageBox2Content'>
        Enter your name:<br />
        <input type="text" style="width:300px;" id="txtInputName" />
        </div>
        <hr />
        <input id='MessageBox2Ok' type='button' value='Save' />
        <input id='MessageBox2Cancel' type='button' value='Cancel' />
    </div>
</ww:wwModalDialog>
```

The content you display can be anything from static text to a complex form layout - that's entirely up to you. It's a Panel so the initial layout is determined by the wwPanel rendering its content. The dialog panel is initially not displayed and activation of the panel is entirely driven through client side code. To activate the dialog from a button click you might use code like this:

```html
<input type="button" id="btnTextEntry" 
		value="Enter Name" onclick="MessageBox2.showDialog();" />
```

MessageBox2 is a client reference with the same name as the control that gets created when you add a wwModalDialog control to the page. Calling .showDialog() on it shows the dialog as it was designed and rendered when the page was rendered.

showDialog() also takes several parameters that are applied if you provided the appropriate HeaderId and ContentId and OkButtonId and CancelButtonId values referring to each of the client side DOM element Ids. Then, I can override the default rendering in client code with this code:

```cs
MessageBox2.showDialog("Eh, what's up, <b>Doc</b>?",
                       "Bugs Bunny"," Ok "," Forget it! ",OnDialogClose);
```

All parameters are optional so you can selectively override elements - pass null for anything you want to leave at default values. Notice that the last parameter is a callback handler that gets called when a button is clicked in the dialog, which overrides the OnClientDialogClick server property. When you click either of the two buttons the handler is called. Here's a somewhat sophisticated response to the modal dialog above that asks for a name: 

```cs
function OnMessageBox2Click(Result,ButtonElement)
{
    var ctl = new wwControl("divNameResult");
    
    if (Result == 1)
    {
        var Name = $('txtInputName').value;
        if (!Name)
        {
            // * ** Don't allow to exit
            ButtonElement.value = "Try again";
            ButtonElement.style.color = "red";
            
            // * ** revert settings after 3 seconds
            setTimeout( function() { ButtonElement.value='Save'; ButtonElement.style.color='black'; },3000);
            return false;  // keep the dialog open
        }
        else
             ctl.setText("Thank you for your input, " +  Name  +"!");
    }
    else
        ctl.setText("You've canceled the dialog!");

    ctl.fadein(3);
    setTimeout( function() { new wwControl('divNameResult').fadeout(); },5000);    
}
```

The handler receives two parameters: A resultcode of 1 or 0 which stands for Ok or Cancel (or more precisely the OkButton, CancelButton whatever you actually label them) and a ButtonElement, which is the button DOM element that was clicked. From the DOM element you can retrieve the .value property to get the text of the button, or the ID to get the button id if you need to.

It's not necessary to implement a handler. Sometimes you just want to display a dialog as a message and in that case you click and it closes - end of story. If you do care about what was clicked the handler lets you check which option was chosen and you can act accordingly. You can also return false from the handler function which tells the control to not release the dialog. You can see this above if the user clicks Save and doesn't enter a name the button caption briefly changes but the dialog is not closed. This can be useful in ensuring that all values have been entered etc.

Remember all of this happens in CLIENT SIDE code! You can set up the content of the dialog with server side code, but activating and displaying the dialog and handling the clicks all happens on the client side in JavaScript.

### Generic Dialogs
The wwModalDialog control requires that you design a dialog layout. It's easy enough to do and you typically will have one modal dialog on a page that you reuse for various different messages you need to display, using showDialog() to assign custom content.

If you don't want to design a dialog explicitly you can also create one on the fly with the static wwModalDialog.messageBox() function and the more flexible createDialog() methods.

The messageBox function is static and creates a dialog for you on the fly. The dialog is generic using standard blue and white dialog colors and a gray background with no options to customize, but it doesn't require any sort of setup. Simply ensure you have the wwScriptLibrary loaded into the server page (use IncludewwScriptLibrary() to load if you don't have an AJAX control loaded already) and then simply call code like this anywhere in your client side page script code:

```cs
wwModalDialog.messageBox("Html Text here<hr>More Text","Title","OK","Cancel",CallbackHandler);
```

The dialog is very generic and looks something like this:

![](IMAGES%5CWebControls%5CControls%5CwwWebModalDialog_Generic.png)

It's not very configurable, it's quick and dirty and gets you the desired operational effect.

For a little more control you can use the createDialogMethod which creates the ame generic dialog but hands it back to you as an object so you can potentially modify the display attributes.

```cs
<script>
function MessageBox(Message,Title)
{
   // * ** Create here inline or make this ref global
   var Mbox = wwModalDialog.createDialog("Mbox",500,"Done","Cancel",MBox_Callback);
   Mbox.show(Message.htmlEncode(),Title.htmlEncode());
}
function MBox_Callback(Result,Button)
{
   if (Result == 1) 
      alert("Ok. Button Text: " + Button.value);
   else
      alert("Cancel. Button Text: " + Button.value);
}
</script>
```