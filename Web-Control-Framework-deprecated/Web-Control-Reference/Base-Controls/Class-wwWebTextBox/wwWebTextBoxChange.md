Change event that is fired if the value of the control is changed. Only fired when AutoPostBack=true.

```html
<ww:wwWebTextBox runat="server" id="txtName"  
            AutoPostBack="true" 
            Change="OnTxtNameChange"  />
```

To handle the event just implement the method on the page object:

```foxpro
DEFINE CLASS Absolute_Page as WWC_WEBPAGE

FUNCTION OnLoad()
ENDFUNC

FUNCTION OnTxtNameChange()
this.lblNameEcho.Text = this.txtName.Text + ". " + TIME()
ENDFUNC

ENDDEFINE
```