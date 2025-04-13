Provides a very basic rich HTML Edit control for Internet Explorer only. For any other browser it displays a text box instead.

This control is lightweight and doesn't have any dependencies except to some images. For a more sophisticated soluion please check out the [wwWebFckEditor control](vfps://Topic/_2440VWFYM)which provides much richer editing and support for Mozilla style browsers.

The control inherits from [wwWebTextBox](vfps://Topic/_1MT08YZJT) and so inherits all of the TextBox's attributes including automatic PostBack and the ability to databind to a ControlSource directly. The control uses a hidden TextArea field to contain the actual HTML text entered into the rich control or the displays the text area for other browsers or when using Source View.

The edit view is presented in scope of the currently active stylesheet for the page so any style settings that the current page has is used within the control. The control is essentially an edit enabled <DIV> tag.

With the editor view in place you can do basic HTML editing tasks - like text markup and alignment, embedding of images and hyperlinks and drag and drop rich text from any source that handles HTML output (like Word or Office applications).

![](IMAGES%5CWebControls%5CControls%5CHtmlEditorControl.png)

The control does not provide complex editing features such as built in table editing support and context menu support for many things.

There are a couple of limitations to this control:
<ul>
* Works in Internet Explorer 5.0 and later only
* Only one of these controls can be placed on a single page
</ul>