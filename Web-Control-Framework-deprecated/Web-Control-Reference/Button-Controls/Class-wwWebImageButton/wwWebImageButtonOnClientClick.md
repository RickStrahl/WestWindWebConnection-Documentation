Allows attaching of a client side JavaScript click handler that fires when the link is clicked. Use return false; to force the click to prevent navigation:

```html
<ww:wwWebImageButton ID="lnkPunchIn" runat="server"                                      
     ImageUrl="images/Punchin.gif"
     NavigateUrl="punchin.tt"
     Text="Punch In" CssClass="hoverbutton"   
     OnClientClick="alert('hello'); return false;"                                   
     />
```