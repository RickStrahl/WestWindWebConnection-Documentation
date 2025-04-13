Once you have created a user control (preferrably with an .ASCX extension so Visual Studio can render it properly)  you can use it on any page. Using of UserControls requires a couple of not quite intuitive steps because you need to provide some additional information about the control that is not automatically set when the control is dropped on the page.

The easiest way to get the control onto the page is to drag the created user control onto the design surface in Visual Studio. When you do this you will see the following at the top of the page:

```html
<%@ Register Src="controls/PageHeader.ascx" TagName="PageHeader" TagPrefix="uc1" %>
```

and this in the location where you dropped the control (invalid code):

```html
<uc1:PageHeader ID="PageHeader1" runat="server" />
```

If you were to try and run the page as it is now you will get an error stating that the PageHeader control cannot be found:

* <blockquote>Parsing of the page default.tt failed.
Couldn't create control: PageHeader1 [pageheader_WCSX].
Error: Class definition PAGEHEADER_WCSX is not found. 
</blockquote>*

The reason for this is that Web Connection has no idea where the control lives at this point, so in order for the control to work you need to provide a couple of additional property settings on the control:

```html
<uc1:PageHeader ID="PageHeader" runat="server" 
                ScriptFile="~/controls/PageHeader.ascx"
                SourceFile="TimeTrakker\PageHeader_Control.prg" />
```

If you run again now Web Connection can find the control's markup file (ScriptFile - a relative path from the current page or a ~ site relative path (shown)) and the PRG file (SourceFile - a relative path from the current FoxPro IDE path).

At this point the control exists on the page and you can access is as this.Page.PageHeader and through it access its properties and values.

### User Controls don't unload!
Note that User Controls get loaded like other controls using SET PROCEDURE TO and then loaded as a class. The class is never released (unlike pages which are) and so User Controls, like built in controls stay permanently loaded in the running application.

This means:

<blockquote>**If you change the markup of a user control you have to stop the application, clear memory and then restart it. IOW, while Page markup can generally be changed and show immediately, User Control markup does not until your unload and restart.**</blockquote>