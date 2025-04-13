Fires if AutoPostBack is set to .T. and you select a new item in the list.

A typical setup looks like this in the Page (using a wwWebListbox concrete instance):

```html
<ww:wwWebListBox runat="server" id="lstStates" 
                 AutoPostBack="True" Change="lstStates_Change" />
```

On the server you then implement:

```foxpro
FUNCTION lstStates_Change()
this.lblSelected.Text = this.lstStates.SelectedIndex
ENDFUNC
```