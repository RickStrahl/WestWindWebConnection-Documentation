This control provides a modal popup dialog by overlaying a transparent pane over the content and displaying an HTML based dialog box.

The control is made up of several components: The dialog itself, a content area where messages are displayed and an ok and optional cancel button that close the 'dialog'.

The control creates a client side instance of the wwModalDialog class which is accessible by the control's name. To invoke the dialog simply call the showDialog() method of this instance with the text to display in the content area.

```html
<ww:wwModalDialog ID="MessageBox" runat="server" 
    ContentId="MessageBoxContent" 
    OkButtonId="MessageBoxClose"
    CssClass="dialog boxshadow" Style="background: white; border-width: 0; position: absolute">
    <div id='MessageBoxHeader' class="dialog-header">Header</div>
    <div id='MessageBoxContent' class="dialog-content">
        <input id='MessageBoxClose' type='button' value='Close Dialog' />
    </div>
</ww:wwModalDialog>
```