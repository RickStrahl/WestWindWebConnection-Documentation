The wwWebImageButton is a control for displaying both pure image links or link and text links. The button can post back to the same page or navigate to a new URL if a NavigateUrl/UrlControlSource is provided. 

As such this control is a hybrid between a wwWebHyperLink, wwWebLinkButton and wwWebImage. Basically you should be able to use this control for any links that require images. The control renders as a hyperlink with the image as its content.

The control renders as:

```html
<a href="punchin.tt" id="lnkPunchIn" class="hoverbutton">
<img src="images/Punchin.gif" id="lnkPunchIn_image" > Punch In</a>
</div>
```

The main control is the div tag and it receives the ID and all base tags. You can use CSS to style the HREF and IMG tags internally.

Control sources of this control map to:
<ul>
* ControlSource - ImageUrl
* UrlControlSource - NavigateUrl
</ul>

For example to cause a custom postback operation to occur (in a Repeater or DataGrid for example):

```html
<ww:wwWebImageButton runat="server" ID="btnDelete" 
                     Click="btnDelete_Click"
                     ImageUrl="~/images/remove.gif"
                     CssClass="hoverbutton"
                     UrlControlSource="this.Page.GetPostbackEventReference('Page','btnDelete_Click',TRANS(pk),.T.)"
/>
```