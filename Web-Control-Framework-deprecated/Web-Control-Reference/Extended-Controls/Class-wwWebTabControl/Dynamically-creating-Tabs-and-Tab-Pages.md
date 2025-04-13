The Web Connection Web Control Framework includes a Tab control, which is essentially a tab strip control. This means that the control itself manages the display of the strip, but doesn't internally manage creation of containers that are displayed as 'pages' for the actual content. However the process to create pages on the fly and associate them with Tabs is relatively straight forward.

To dynamically create tabs and tab pages you can start with a set up like the following in HTML markup:

```html
<body style="margin-top:0px;margin-left:0px">
    <form id="form1" runat="server">

    <ww:wwWebErrorDisplay runat="server" id="ErrorDisplay" />

    <h1>Tab Test</h1>

    <div class="containercontent">
        <ww:wwWebTabControl runat="server" ID="Tabs">        
        </ww:wwWebTabControl>

        <ww:wwWebPanel runat="server" id="TabContainer" 
                       OverrideNamingContainer="true">                
        </ww:wwWebPanel>
    </div>
    
    </form>
</body>
```

The wwWebTabControl and the wwWebPanel controls are an empty Tab Control and a Panel, which will serve as a location to add additional Tabs and Pages to the page. 

To create tabs and pages dynamically you can now use code to add tabs to the tab control and panels to the tab container in the form of Panel controls. The following code creates 5 tabs and 5 panels linked to those tabs with basic content:

```foxpro
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ****
* Page :: OnLoad
* *** *** *** *** *** *** *** *** *** *** *** *** ***
FUNCTION OnLoad()

FOR lnX = 1 TO 5
	lcX = TRANSFORM(lnX)
	
	* ** Start by adding a new tab
	this.Tabs.AddTab("Page " + lcX,"","Page" + lcX)
	
	* ** Create a panel that will hold our content
	loPanel = CREATEOBJECT("wwWebPanel")
	loPanel.Id = "Page" + lcX
	loPanel.cssClass = "tabpage"
	
	* ** Add some content to the panel
	* ** Note you could also add a composite user control here
	loLabel = CREATEOBJECT("wwWebLabel")
	loLabel.Text = loPanel.id
	
	* ** And add the label to the Panel
	loPanel.AddControl(loLabel)
	
	* ** Add our new Panel to the TabContainer
	this.TabContainer.AddControl(loPanel)	
ENDFOR
ENDFUNC
* Onload
```


The code is pretty straight forward. It basically creates a new tab for each iteration of the for loop. It also creates a new panel as well via CreateObject("wwWebPanel") and we then add some content into that panel - a label in this case which is then added to the panel via AddControl. As you probably know the Web Control Framework is based on content containers that can hold content so everything you want to add to the page somehow must be a control. A label can hold anything - usually HTML or you can use literal controls for raw HTML if you don't want to style the text. But you can also add a more complex control into the panel - for example a user control that contains page content that was pre-created in the designer and with other controls/expressions embedded, or even a custom composite server control that generates meta driven data (which is what I'm doing at the moment with a customer and what brought this up). Lots of options for generating the content into the page.

Finally, once the panel's been created the new panel needs to be added to the page by call this.TabContainer.AddControl(loPanel) which completes the addition of the panel and its content to the page. AddTab() links to this panel via its third lcClientID parameter which then manages the initial display and activation of the tab page.