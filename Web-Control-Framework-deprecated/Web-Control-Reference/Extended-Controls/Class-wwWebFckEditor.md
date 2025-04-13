The wwWebFckEditor control a thin wrapper around the popular <a href="http://www.fckEditor.net" target="top">FCK Editor control</a>. The  control provides rich text editing for Internet Explorer and Mozilla style browsers and includes many advanced features for text and image management.

![](IMAGES%2FWebControls%2FwwWebFckEditor.png)

The key properties of the control are the Text property which is used to set and retrieve the value of the editor and the [EditorBasePath](vfps://Topic/_S8X02FE4U) which controls where the FCK Library is found.

The control works pretty much like a TextBox control so you can use the Text property to set and read its value and use ControlSource databinding to bind values to it.

The Web Connection control wrapper provides only basic setup features to get the control onto a page easily. If you need to highly customize the operation of the control you will have to use FCK Editor's own configuration APIs and options to do so. 

```html
<ww:wwWebFckEditor runat="server" id="txtMessageFck" Width="95%" Height="400px" Visible="false" 
        EditorBasePath="~/fckEditor/" 
        FontNames="Verdana;Tahoma;Arial;Comic Sans MS;Arial Black;Courier New,Monospace;"
        Theme="office2003" 
        ControlSource="this.Page.oMessage.Message"                       
        ErrorMessageLocation="2" ClientOnLoad="FckLoaded"
/>
```