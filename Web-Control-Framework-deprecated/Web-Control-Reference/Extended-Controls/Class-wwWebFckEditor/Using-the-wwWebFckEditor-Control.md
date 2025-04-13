Using the control is quite simple: Simply drag the control onto the page and set properties. In the designer the control doesn't show a rich display because all the display features of FCK editor are dynamically created with JavaScript so the designer only shows a rough simulation of a square box with a header.

![](IMAGES%2FWebControls%2FwwWebFckEditorDesign.png)

The first thing to do is to ensure that FCK Editor is installed and then specify the install location with the BaseEditorPath. The default is ~/fckEditor/ which assumes that fckEditor is installed in the Web virtual. You can point at another virtual path as necessary.

A typical markup layout may look like this:
```html
<ww:wwWebFckEditor runat="server" id="txtComments" Width="100%" Height="400px" 
                        EditorBasePath="~/webcontrols/fckEditor/" 
                        FontNames="Verdana;Tahoma;Arial;Comic Sans MS;Arial Black;Courier New,Monospace;"
                        Theme="office2003"                       
                        ControlSource="this.Page.oEntry.oData.Comments"
                        ClientOnLoad="FckLoad">
This is a test of the emergency broadcast system                                            
</ww:wwWebFckEditor>
```

The wwWebFckEditor control inherits from wwWebTextbox so in terms of behavior it's very similar to a standard textbox. You use the Text property for setting and reading the value of the input text. And you can use control source binding to bind the value directly to the editor control.

The control renders using using skins (which can be spefied with the Theme property) and you can switch easily between the different themes. The following uses the office2003 Theme:

![](IMAGES%2FWebControls%2FwwWebFckEditor.png)

There are a few customizations you can make through the control which allow you to switch Themes, ToolbarSets (Default and Basic are provided and you can create custom ToolbarSets) and you can customize the Style, Format, Font and Size drop downs via the StylesXmlPath, FontFormats, FontNames and FontSize properties.

FCK loads the editor in an IFRAME that's separate from your main page so one common thing you'll want to provide is a style sheet for the editor so that the user editing the content sees the content with the same styles as the page (assuming that's the intent). You can use EditorAreaCSS to point at a virtual path for a CSS file to provide this CSS. The control defaults to the ~/westwind.css file so all the Web Connection styles are available but you can of course apply your own style sheet.

The StylesXmlPath property points at an XML file that contains style information which shows in the dropdowns that is based and defaults to fcktemplates.xml in the FCK install folder. This allows you set up custom styles that are applied to certain types of elements.

FontNames, FontSizes are simply lists of all the fonts and sizes that you would like to allow. The defaults for these are probably sufficient so typically you can leave this blank. The FormatNames is a list of HTML standard paragraph formatting elements like H1-H6, Pre etc.. Unfortunately there's no direct support to pick up 

There's not much else to think about with this control. The main complexities arise with custom configuration if you want to make the editor perform special tasks or provide a different look which is handled using standard FCK configuration mechanisms. The control provides only basic configuration features, with the more advanced options described in the [Configuring FCK Editor](vfps://Topic/_2440Z0VML) topic.