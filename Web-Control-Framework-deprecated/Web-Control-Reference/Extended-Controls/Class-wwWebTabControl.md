The wwWebTabControl is an easy to use TabStrip control that allows you to break large amounts of content into separate pages. The control works via client script to manage and activate specified HTML containers dynamically when a tab page is selected. To specify tab 'pages' that are to be activated on tab clicks you specify a client DOM id for the element to be shown or hidden appropriately.

At runtime the tab control looks like this (with default styles applied):

![](IMAGES%2FWebControls%2FControls%2FwwWebTabControl.png)

The tab control consists only of a tab strip, which determines various display characteristics and a collection wwWebTabPages that define the behavior for each tab button when clicked. The idea is that a tab button is associated with an HTML DIV element that is activated when the button is clicked and hidden when another tab is activated.

The following markup gives a good idea of the structure for the control:
```html
<ww:wwWebTabControl runat="server" id="Tabs" 
                    SelectedTab="divDisplay" TabHeight="30" TabWidth="120">
    < TabPages>
        <ww:wwWebTabPage runat="server"  
			TabPageClientId="divInfo" 
			Text=" Information" 
			TabImage="images/info.gif" 
			Style="text-align:left" ActionLink="" > 
		</ww:wwWebTabPage>
		<ww:wwWebTabPage runat="server" 
			TabPageClientId="divSelection" 
			Text="Selection" ActionLink="" 
			TabImage="">
		</ww:wwWebTabPage>
		...
    < /TabPages> 
</ww:wwWebTabControl>
```

<div class="notebox">**Tab Page Display Styling**  
Please note that this control by default relies on a default display styles configured in westwind.css. This format uses default styles for .tabbutton, .selectedtabbutton and .tabdisabledbutton which in turn use 120 pixel wide images to display the tabs. The display is fully customizable by customizing these styles or assigning your own custom styles to the control.</div>

<div class="notebox">**Client Side Tab Selection**  
You can also activate tabs on the client side via JavaScript with the static ActivateTab() function. 

```javascript
ActivateTab("TabControl1","Page 1 Caption")
```

You pass the Client Control Id and the name or tab index as parameters. ActivateTab() is generated into any page that contains a Tab Control so you can use client script to change pages.
</div>

For more information see [How the wwWebTabControl works](vfps://Topic/_23V13RVNQ).