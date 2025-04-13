A WebControl application is based on the concept of WebControls. Each control in this framework is a self contained unit that knows how to display itself and process its own event messages and act upon them, updating its control state. 

### wwWebControl is the base for all other controls
Every object in the framework - including the top level wwWebPage container - is based on the wwWebControl class, which provides much of the core processing and behavior for most controls.

The framework is based on a hierarchy of controls. The basis of all controls is the WebControl base class which provides the core functionality. All other controls are based on the wwWebControl class, wwWebTextBox, wwWebCheckbox, wwWebDropDownList etc. all inherit a common set of functionality and behaviors. In addition there are a set of list classes - wwWebDropDownList, wwWebListBox, wwWebRadioButtonList - which inherit from wwWebListControl that provides sub-item functionality.

![](IMAGES%5CWebControls%5CHowItWorks%5CWebControls.jpg)

wwWebControl implements many common properties. There are display properties like Width, Height, Style, CSSClass. There are databinding related properties like ControlSource and ControlSourceFormat, there are raw HTML properties like PreHtml and PostHtml. The control also implements a lot of stock behavior such as automatically managing ViewState and provides methods that can provide easy implementation of reading POST data and performing databinding.  It's important to understand that every control can choose to completely override these behaviors, or simply pick and choose which features to use and which one to leave alone.

The important thing to understand about controls is that they are a self contained unit and the control and the control alone is responsible for creating output and managing its internal state. Controls are self-contained and designed to work outside of the Page Framework so you can use the controls in standard Web Connection Process code for rendering output. In this scenario, you can simply set a few properties and call the Render() method to generate the output.

Render() is the key method for most controls that's responsible for generating the final output for each control and for most of the controls in the framework this method does 90% of the work. However, there are many other methods and events that all work together if you use the controls inside of the Page Pipeline.

### Containership is key
Each control implements its own functionality, but by themselves the controls are fairly limited. While you can set properties and call the Render() method on all controls, the real power of the controls isn't realized until they are 'activated' via events that fire through the Page Pipeline.

The Page Pipeline works through containership. Everytime a control is added the control is added to the parent's ChildControls collection and the added control gets a reference to the ParentControl. Through this model the parent control can reference all children and fire events on it. And the childcontrol always has access to its immediate parent control as well as the Page object, which provides many services like ViewState and ClientScript registration features for example.

The following figure shows the control containership model.

![](IMAGES%5CWebControls%5CHowItWorks%5CControlContainerShip.jpg)

Note that the wwWebPage contains most of the other controls. wwWebForm is a special 'control' that provides the Postback form control and its end tag. Most controls are simple controls like TextBox, ListBox, but there are also a couple of container controls like wwWebDataGrid which can contain Column controls, and the Panel control which can contain any number of controls.

The containership is managed through FoxPro code which looks like this:

```foxpro
DEFINE CLASS HelloWorld_Page_WCSX AS HelloWorld_Page

   Id = [HelloWorld_Page]

   * ** Control Definitions
   form1 = null 
   txtCompany = null 
   btnSubmit = null 
   btnChangeColor = null 
   lblMessage = null 

FUNCTION Init(loPage)
DODEFAULT(loPage)


__lcHtml = []
__lcHtml = __lcHtml + []+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [	 <html>]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [	 <head>]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [		  <title>Hello World Demo</title>]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [		  <link href="westwind.css" rel="stylesheet" type="text/css" />]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [	 </head>]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [	 <body>]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + []
_1LZ06ZN6P = CREATEOBJECT("wwWebLiteral",THIS)
_1LZ06ZN6P.Text = __lcHtml
THIS.AddControl(_1LZ06ZN6P)


THIS.form1 = CREATEOBJECT("wwWebform",THIS)
THIS.form1.Id = "form1"
THIS.AddControl(THIS.form1)


__lcHtml = []
__lcHtml = __lcHtml + []+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [				<div>]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [					 <h1>Hello World Demo</h1>]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [					 <p>]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [						  <a href="default.htm">Demo Home</a></p>]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [					 <br />]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [					 Please enter a Customer Name:<br />]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + []
_1LZ06ZN6R = CREATEOBJECT("wwWebLiteral",THIS)
_1LZ06ZN6R.Text = __lcHtml
THIS.AddControl(_1LZ06ZN6R)


THIS.txtCompany = CREATEOBJECT("wwwebtextbox",THIS)
THIS.txtCompany.Id = "txtCompany"
THIS.txtCompany.Width = [252px]
THIS.AddControl(THIS.txtCompany)


__lcHtml = []
__lcHtml = __lcHtml + [&nbsp;]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + []
_1LZ06ZN6T = CREATEOBJECT("wwWebLiteral",THIS)
_1LZ06ZN6T.Text = __lcHtml
THIS.AddControl(_1LZ06ZN6T)


THIS.btnSubmit = CREATEOBJECT("wwwebbutton",THIS)
THIS.btnSubmit.Id = "btnSubmit"
THIS.btnSubmit.Text = [Go]
THIS.btnSubmit.Width = [80]
THIS.btnSubmit.HookupEvent("Click",THIS,"btnSubmit_Click")
THIS.AddControl(THIS.btnSubmit)


__lcHtml = []
__lcHtml = __lcHtml + [&nbsp;]
_1LZ06ZN6W = CREATEOBJECT("wwWebLiteral",THIS)
_1LZ06ZN6W.Text = __lcHtml
THIS.AddControl(_1LZ06ZN6W)


THIS.btnChangeColor = CREATEOBJECT("wwwebbutton",THIS)
THIS.btnChangeColor.Id = "btnChangeColor"
THIS.btnChangeColor.Text = [Change Color]
THIS.btnChangeColor.HookupEvent("Click",THIS,"btnChangeColor_Click")
THIS.AddControl(THIS.btnChangeColor)


__lcHtml = []
__lcHtml = __lcHtml + [&nbsp;]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [					 <br />]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [					 <br />]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + []
_1LZ06ZN6Z = CREATEOBJECT("wwWebLiteral",THIS)
_1LZ06ZN6Z.Text = __lcHtml
THIS.AddControl(_1LZ06ZN6Z)


THIS.lblMessage = CREATEOBJECT("wwweblabel",THIS)
THIS.lblMessage.Id = "lblMessage"
THIS.lblMessage.Attributes.Add("size","20")
THIS.AddControl(THIS.lblMessage)


__lcHtml = []
__lcHtml = __lcHtml + [</div>]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + []
_1LZ06ZN71 = CREATEOBJECT("wwWebLiteral",THIS)
_1LZ06ZN71.Text = __lcHtml
THIS.AddControl(_1LZ06ZN71)


_1LZ06ZN72 = CREATEOBJECT("wwWebForm",THIS)
_1LZ06ZN72.RenderType = 2
THIS.AddControl(_1LZ06ZN72)

__lcHtml = []
__lcHtml = __lcHtml + []+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [	 </body>]+CHR(13)+CHR(10)
__lcHtml = __lcHtml + [	 </html>]
_1LZ06ZN73 = CREATEOBJECT("wwWebLiteral",THIS)
_1LZ06ZN73.Text = __lcHtml
THIS.AddControl(_1LZ06ZN73)


ENDFUNC
ENDDEFINE
```

Everything on a WebPage - including literal text - is an object.  The above class was autogenerated from a script page that uses declarative syntax to describe this same page. The page looks like this:

```html
<%@ Page Language="C#" 
	ID="HelloWorld_Page"
	GeneratedSourceFile="controldemo\Helloworld_page.prg"
%>
<%@ Register Assembly="WebConnectionWebControls" Namespace="Westwind.WebConnection.WebControls"
    TagPrefix="ww" %>
    <html>
    <head>
        <title>Hello World Demo</title>
        <link href="westwind.css" rel="stylesheet" type="text/css" />
    </head>
    <body>
        <form id="form1" runat="server">
            <div>
                <h1>Hello World Demo</h1>
                <p>
                    <a href="default.htm">Demo Home</a></p>
                <br />
                Please enter a Customer Name:<br />
                <ww:wwWebTextBox ID="txtCompany" runat="server" Width="252px"></ww:wwWebTextBox>&nbsp;
                <ww:wwWebButton ID="btnSubmit" runat="server" Text="Go" Width="80" Click="btnSubmit_Click" />&nbsp;
                <ww:wwWebButton ID="btnChangeColor" runat="server" Text="Change Color" Click="btnChangeColor_Click" />&nbsp;
                <br />
                <br />
                <ww:wwWebLabel ID="lblMessage" runat="server" 
                                size="20"></ww:wwWebLabel>
            </div>
        </form>
    </body>
    </html>
```

The advantage of the markup format is that it can also be edited in an ASP.NET compatible environment like Visual Studio 2005:

![](IMAGES%5CWebControls%5CFirstControls.png)

Web Connection includes a WebPageParser class that is integrated to automatically convert the markup code to the VFP code shown above. 

In this example the page is fairly one dimensional as there are no other containers on the form. You could however have a contained panel control that in turn contains other controls. Once the controls are available as a class however, it's becomes very easy to reference them directly and assign properties to them.



### Master of Ceremonies: wwWebPage
The master container is the wwWebPage object. The Page object is the start of all operations that sits of the top of the hierarchy. It in turn contains other controls, which then in turn can contain other controls. Typically a WebPage contains controls like Labels, TextBoxes, Checkboxes etc. But it can also contain container objects such as Panels that can in turn contain other controls. This is an important aspect of the framework as it allows you to build composite controls that contain a lot of functionality. In addition, container controls make it real easy to show and hide blocks of controls.

The Page object is the master event object in that is starts the  [event sequence](vfps://Topic/_1MB01UF4D) at the top level of the containership hierarchy. It's Run() method fires off an event sequence that activates each of the controls on the form by delegating the events to each of the child controls.

Every event fired on the Page also delegates this same event to its child controls. So when OnLoad fires on the Page it also pushes this event down to the textboxes and checkboxes and labels on the form, which may or may not have code in these event implementations. If there's a container control that receives an event from the Page it will then delegate the event down to its child events in turn. 

This simple mechanism makes it possible to build a sophisticated framework of controls that all respond to a common set of events in a consistent order. As a developer building a top level application, this happens all behind the scenes. But as a control developer you have the ability to hook into the events and override or modify existing functionality, which provides incredibly powerful access to the page processing.