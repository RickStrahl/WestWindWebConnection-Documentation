Web Connection's Web Control framework is fully extensible so you can create new controls quite easily. On a high level the process works like this:
<ul>
* Create a new control based on wwWebControl or any other wwWebControl derived control
* Create a new C# or VB.NET project in Visual Studio for your custom control
* Add a .NET control implementation for your control
</ul>

The last two steps involve working with Visual Studio and .NET. The process for these tasks is pretty basic however, so even if you're not familiar with .NET you should be able to do this fairly easily by using the existing controls contained in the shipped WebConnectionWebControls project as a template.

### Creating the WebControl in FoxPro code
This is the main step for control creation. The FoxPro class determines the runtime behavior of the control. To create a new control you create a class that inherits from WebControl or from any other of the existing WebControl implementations like wwWebTextBox, wwWebDataGrid etc..

Let's create a simple control called wwWebTimeLabel. This control displays the current time or the number of elapsed seconds since the page was loaded as a custom label. This control is essentially a slightly fancy label that uses a JavaScript script timer to redraw its content at a given interval. 

<blockquote><small>**Note:**  
To demonstrate the whole process I'm going to inherit this control from wwWebControl, but it would actually be slightly easier to inherit this control from wwWebLabel. Using wwWebControl as a base class means you have to implement all functionality on your own and that's the idea of this walkthrough.</small>
</blockquote>

Start by creating a new PRG file for your custom class or classes. I created MyCustomControls.prg. You can store it anywhere, but I suggest you store it in a seperate directory where you keep all your Web Connection customizations. Here's the class implementation:

```foxpro
SET PROCEDURE TO WebControl
SET PROCEDURE TO WebControls
SET PROCEDURE TO wwCollections

* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ***
DEFINE CLASS wwWebTimeLabel AS wwWebControl
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ***
#IF .F.
* :Help Documentation
* :Topic:
Class wwWebTimerDisplay

* :Description:
Simple label control that display some text plus an increasing
time value of either a full date string or elapsed seconds since
the page was loaded

* :ENDHELP
#ENDIF

* ** Custom Properties
Text = "Time: "

* ** Determines how frequently the time value is updated in milliseconds
UpdateInterval = 1000

* ** Time Display Mode: Time: Show Time - Seconds: Seconds since page loaded
TimeDisplayMode = "Time"   

* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
* wwWebTimeLabel ::  OnPreRender
* *** *** *** *** *** *** *** *** *** *** *** *** ***
* **  Function:
* **    Assume:
* **      Pass:
* **    Return:
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
FUNCTION OnPreRender()
LOCAL lcScript

IF this.TimeDisplayMode = "Time"
	* ** Generate the JavaScript that updates the Text display
	TEXT TO lcScript TEXTMERGE NOSHOW
	function RefreshDate(loCtl)
	{
		loCtl.innerHTML = "< <this.Text> >" + new Date().toLocaleString();
	}
	ENDTEXT
	this.Page.ClientScript.Add("wwWebTimerDisplay_Time",lcScript)

	* ** Set up the timer to update this script every second
	this.Page.StartupScript.Add("wwWebTimerDisplay",[window.setInterval("RefreshDate(document.getElementById('] + ;
							this.UniqueID + ['))",] + TRANSFORM(this.UpdateInterval) + [);])

ELSE
	* ** Generate the JavaScript that updates the Text display
	TEXT TO lcScript TEXTMERGE NOSHOW
	var TimeLoaded = new Date();
	function RefreshDateSeconds(loCtl)
	{
		loCtl.innerHTML = "< <this.Text> > " + ((new Date().getTime() - TimeLoaded.getTime()) / 1000).toFixed() + " seconds";
	}
	ENDTEXT
	this.Page.ClientScript.Add("wwWebTimerDisplay_Seconds",lcScript)

	* ** Set up the timer to update this script every second
	this.Page.StartupScript.Add("wwWebTimerDisplaySeconds",[window.setInterval("RefreshDateSeconds(document.getElementById('] + ;
							this.UniqueID + ['))",] + TRANSFORM(this.UpdateInterval) + [);])

ENDIF

* ** Not really necessary since there are no child controls
* ** For any Container control this is essential!!!
DoDefault()  

ENDFUNC
*  wwWebTimerDisplay ::  OnPreRender


* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
* wwWebTimeLabel ::  Render
* *** *** *** *** *** *** *** *** *** *** *** *** ***
* **  Function:
* **    Assume:
* **      Pass:
* **    Return:
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
FUNCTION Render()
LOCAL lcOutput

IF this.Visible = .F.
	RETURN
ENDIF

* ** Get basic tags attributes like ID width etc
lcBaseTags = this.WriteBaseTags() 

* ** write an empty <span> tag into the document - the JavaScript
* ** will update the tag on load
lcOutput = "<span " + lcBaseTags + "></span>"
	
RETURN this.PreHtml + lcOutput + this.PostHtml
ENDFUNC
*  wwWebTimerDisplay ::  render


ENDDEFINE
* EOC wwWebTimerDisplay
```

The control implementation is very simple. The core functionality of any control is the Render() method which is responsible for rendering the final output of the control. This control is pretty simple and only renders an empty <SPAN> tag on the page. 

Notice the call the WriteBaseTags() which writes out common control settings including the ID, colors, size, styles and a number of other tags. This is useful so that your control can automatically inherit all of these things without having to write out colors, sizes etc. individually. WriteBaseTags() is the most highlevel of these methods in the wwWebControl class. More low level versions can write out portions of all of this data. Check the various WriteXXX() methods in the wwWebControl class documentation.

Most of the useful stuff that happens is in JavaScript which is dynamically added to the page in the code shown in OnPreRender(). Basically there's a script method added that updates the label text for the two supported display modes. One more displays the current date time every second. The other displays the elapsed seconds since the page was loaded. To get the timer started the timer needs to be set off once the page has completed loading which is done with the this.Page.StartupScript collection to which a call to window.setInterval is added. setInterval is essentially a timer that fires every second (or whatever you specify in the Interval property).

Note that I added two properties to the control:

```foxpro
* ** Determines how frequently the time value is updated in milliseconds
UpdateInterval = 1000

* ** Time Display Mode: Time: Show Time - Seconds: Seconds since page loaded
TimeDisplayMode = "Time"
```

These new properties are accessible in the ASPX page markup as attributes. 

Assuming your control implementation works the control is now ready to be embedded into a page. The first thing you need to make sure of is that Web Connection can find your class so make sure you add MyCustomControls.prg into your Server's OnLoad code:

```foxpro
SET PROCEDURE TO MyCustomControls ADDITIVE
```

Note if you forget this WebPageParser will not be able to compile the page that contains the control properly.

Now you're ready to stick the control into your test page. You can add the control to a page like this:

```html
<ww:wwWebTimeLabel runat="server" id="lblElapsed" 
                   Text="Elapsed Time: " Interval="2000" TimeDisplayMode="Seconds" />
```

Notice that I can simply reference the new properties here and these properties will get assigned the values specified in this markup. Run the page and you should see a label popping up after the initial interval and the label should then update every two seconds. 

### What about Visual Studio?
That was easy! But if you go into Visual Studio and designmode you'll notice that the control is not showing up. Instead you get an ugly indescript grey box with a nasty error message. Intellisense on the control is also not available so you get not easy typing support for your custom properties.

This all makes sense. The control runs just fine, because the FoxPro code for the control is all there.  We've created a new FoxPro control, but VS.NET has no idea this control exists. So in order to get VS.NET to display the control, we'll have to create a .NET Custom Control. This requires us to write some .NET code, but this code is very straight forward. Basically what we need to do in .NET is:

<ul>
* Create a new Control Library
* Create a new Control
* Implement all the custom properties we require
* Implement a Render() method to create the designer display 
</ul>

That sounds like a lot of work, but it's actually quite easy to do, especially if you use the Web Connection Web Controls project as a template. Pick a control that is close to yours and use that class as a base template.

Note that here I chose to create a new control that inherits from wwWebControl, which is the lowest level of subclassing. If you choose you can also subclass from stock ASP.NET controls or from the Web Connection .NET controls.

Ok, let's create a design time control for the wwWebTimeLabel control. First thing we need to do is create a C#  new Class Library project in Visual Studio and add it to a solution. It's easiest to add this to an existing Web Connection Web project - I'm using the WebLog sample here.

![](IMAGES%5CWebControls%5CCreateControls%5CNewControlProject.png)

![](IMAGES%5CWebControls%5CCreateControls%5CNewControlProjectDialog.png)

Your project should now look something like this:

![](IMAGES%5CWebControls%5CCreateControls%5CNewControlProjectSolution.png)

(the WebConnectionWebControls project is not required for this demo, but I recommend you load it into your project anyway so you can subclass the controls from there easily).

**Add references to System.Web and System.Drawing**  
In order to create an ASP.NET control we'll need to add references to System.Web and System.Drawing which are required for controls to load and render. To do this:

Go to the references node in your Control project<ul>

* Right Click
* Add Reference 
* Select System.Web, System.Drawing and for good measure System.Design
</ul>
Your project should now look like this:

![](IMAGES%5CWebControls%5CCreateControls%5CProjectReferences.png)


**Create your class**  
Next remove Class1.cs from the project and add a new class called wwWebTimeLabel.cs:
<ul>
* Right Click | Add New Item
* Select Class from the list of Items
* Type wwWebTimeLabel.cs for the file name and click OK
</ul>

Change the basic code in the class to look something like this:

```cs
using System;
using System.Drawing;
using System.Collections;
using System.ComponentModel;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using System.Drawing.Design;

namespace Westwind.WebConnection.MyWebControls
{
    public class wwWebTimeLabel : WebControl
    {

    }
}
```

The namespace is really up to you - it will show up in the @Register element definition on the page that hosts one or more of these controls. The other changes basically add some namespaces that you're likely to be using in your code.

### Adding Properties
The next step is to add the custom properties to your control. For the wwWebTimerLabel control we need to add two new properties to the control: 
<ul>
* Interval
* DisplayMode
</ul>

DisplayMode is basically a multiple value selector - in FoxPro just a string. But in .NET we can use an Enumerator for this. So at the bottom of the page before the last } add an Enum definition:

```cs
namespace Westwind.WebConnection.MyWebControls
{
    public class wwWebTimeLabel : WebControl
    {

    }

    public enum DisplayModes
    {
        Time,
        Seconds
    }
}
```

Next let's add the properties:

```cs
public class wwWebTimeLabel : WebControl
{
        [Description("The text for the label displayed before the time value."),DefaultValue("Time: "),
         Category("Timer")]
        public string Text
        {
            get { return _Text; }
            set { _Text = value; }
        }
        private string _Text = "Time: ";

    [Description("Determines how often the time value refreshes"),DefaultValue(1000),
     Category("Timer")]
    public int Interval
    {
        get { return _Interval; }
        set { _Interval = value; }
    }
    private int _Interval = 1000;


    [Description("The message displayed in front of the time value"), DefaultValue(DisplayModes.Time),
     Category("Timer")]
    public DisplayModes DisplayMode
    {
        get { return _DisplayMode; }
        set { _DisplayMode = value; }
    }
    private DisplayModes _DisplayMode = DisplayModes.Time;
}
```

Note that in this control I have to implement the Text property because a plain WebControl class doesn't have a Text property. However, if I had inherited from Label, Text would already exist and I could simply implement a constructor that sets the Text default value to the same value as our FoxPro default value.

Note the [] Attributes that determine some of the designer behaviors for the control. Description is text that gets displayed as help text in the designer. Default Value is important too - this value should match the private default value and should also match the default value of your control. When the default is set, VS.NET doesn't insert the text into the HTML markup which means the default is used - so it's important that your control uses consistent default values in both FoxPro and here in the .NET designer control. 

**Implementing the Rendering**  
The final thing left to do is create the Render() method so we have something the designer can display in designmode. Now again - this would have been a lot easier if I had inherited from the ASP.NET Label in which case all I would have to do is set the text property and call the base.Render() method which would then appropriately render the text. But to demonstrate let's do this from scratch so you can see what is involved in generating your output manually.

The Render() method is the key method that is used to render output in ASPX pages and in the designer. It uses an HtmlWriter object that is passed in that you can write to with the Write() method. You can simply fire strings into the writer and this output gets rendered.

Here's a simple implementation:

```cs
protected override void Render(HtmlTextWriter writer)
{
    
    string TimeValue = DateTime.Now.ToString();
    if (this.DisplayMode == DisplayModes.Time)
        TimeValue = "2 seconds";

   writer.Write("<span id='" + this.ID + "' class='" +  this.CssClass + 
                         "' style='color:" + this.ForeColor.ToString() +
                         ";background:" + this.BackColor.ToString() + ";'" +
                         ">" + this.Text + " " + TimeValue + "</span>");

}
```

Notice that based on the property settings I'll render a slightly different display. The VS.NET designer refreshes and calls the Render method of this control for every change made to the properties of the control so the changes show up in the designer immediately! This makes it possible to build some fairly sophisticated control displays in the designer.

The code above is very simple though: It merely renders a <span> tag and adds a couple of style settings that are likely going to be set.

If you have problems compiling your class you can use the complete source at the end of this topic.

Compile this code. Once compiled you have now created a DLL assembly that can be loaded into the toolbox of VS.NET. To add it you can simply go into the toolbox, right click add tab. Then once the tab exists right click on the content and Add Controls.

But you might want to hold of on that step. We'll want to make changes to these controls, and there's actually an easier way to get the controls loaded in the designer.

**Adding a reference to the Control Library in your Web Project**  
You can add a project reference to your Web Project. To do this:

<ul>
* Select your Web project's BIN directory
* Right Click | Add Reference
* Select Projects Tab and select your web control project
</ul>

![](IMAGES%5CWebControls%5CCreateControls%5CAddReferenceToMyControlLibrary.png)

Now, go into the visual designer of a Web Page. You may have to right click and click Refresh to reload the page and its environment. Open the toolbox and you should now see an automatic tab for your MyWebControls project:

![](IMAGES%5CWebControls%5CCreateControls%5CAutoProjectControls.png)

Go ahead and drop the control on to your Web Page. You should see the control rendering with the output we've generated. Try changing the Text and the DisplayMode and notice how the control is rendering in the designer. Nothing fancy but it works!

![](IMAGES%5CWebControls%5CCreateControls%5CControlOnTheForm.png)

If you look at the Property Sheet you'll find that our custom controls are there and they can be modified as expected including a dropdown for the DisplayModes:

![](IMAGES%5CWebControls%5CCreateControls%5CPropertySheet.png)

Cool, n'est pas?

**Refining the Control for the Designer**  
There are couple of things you can do make this control work a little nicer in the designer. For one, if you look at the properties displayed in the property sheet, there are a number of properties that Web Connection doesn't respect or render and it's a good idea to hide these values.

To do this you can go into the control and override the properties setting the [Browsable(false)] attribute to force the designer to not show these properties in the designer.

A common set of default properties to ignore are:

```cs
#region * ** Overriden hidden properties
[Browsable(false)]
public override string SkinID
{
    get
    {
        return base.SkinID;
    }
    set
    {
        base.SkinID = value;
    }
}

[Browsable(false)]
public override bool EnableTheming
{
    get { return _EnableTheming; }
    set { _EnableTheming = value; }
}
private bool _EnableTheming = false;


[Browsable(false)]
public override Color BorderColor
{
    get
    {
        return base.BorderColor;
    }
    set
    {
        base.BorderColor = value;
    }
}

[Browsable(false)]
public override short TabIndex
{
    get
    {
        return base.TabIndex;
    }
    set
    {
        base.TabIndex = value;
    }
}

[Browsable(false)]
public override BorderStyle BorderStyle
{
    get
    {
        return base.BorderStyle;
    }
    set
    {
        base.BorderStyle = value;
    }
}

[Browsable(false)]
public override Unit BorderWidth
{
    get
    {
        return base.BorderWidth;
    }
    set
    {
        base.BorderWidth = value;
    }
}
#endregion
```

Compile - now if you go into the designer and look at the property sheet it'll look a lot leaner and more appropriate for your control:

![](IMAGES%5CWebControls%5CCreateControls%5CPropertySheetFixed.png)

Finally, it's also a good idea to set some default attributes on the class itself so that class can show a custom icon (or at least a more appropriate icon then the generic control icon) and allow a default insertion signature.

```cs
[ToolboxBitmap(typeof(Label)),
DefaultProperty("Text"),
ToolboxData("<{0}:wwWebTimeLabel runat='server'/>")]
public class wwWebTimeLabel : WebControl
```

Now, if you load the control into the toolbox ('real' loading not with the project refernce though) you will see the customized icon. 

**Designer Controls Summary**  
And voila, there you have it. Your first user control. The process to create this is not exactly trivial. Especially if you are not familiar with .NET. But you can use the existing controls in the WebConnectionWebControls project as a guideline. There are lots of different scenarios covered for the control logic.

I also want to remind you that you should try to reuse functionality as much as possible especially in the designer control. In the designerControl the easiest thing for this sample would have been to subclass from wwWebLabel and simply set the Text property to the value we want, the call base.Render(); to let the label handle the actual rendering. The code for this would have simply been:

```cs
protected override void Render(HtmlTextWriter writer)
{
    this.Text = this.Text + " " + TimeValue;
    base.Render(writer);
}
```

The base label handles the proper display and class attributes etc. so your code doesn't have to. Use existing Web Connection controls to subclass from or even ASP.NET controls if the display is appropriate in the designer. Often times the main thing is getting the property values - the display is really an esoteric thing - you want something to display but it doesn't necessarily have to match the actual display exactly. A good example is the wwWebHtmlEditor control - you can't display the actual editor in the designer, so a rough placeholder is displayed instead. It's nice to have accurate visual display in the designer, but it's not that crucial. If anything make sure that page placement (height, width, colors, styles, class are Ok) but beyond that it's up to you to decide how much you want to implement.

**Complete Source Code for the wwWebTimerLabel Control**  
```cs
using System;
using System.Drawing;
using System.Collections;
using System.ComponentModel;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using System.Drawing.Design;

namespace Westwind.WebConnection.MyWebControls
{
    [ToolboxBitmap(typeof(Label)),
    DefaultProperty("Text"),
    ToolboxData("<{0}:wwWebTimeLabel runat='server'/>")]
    public class wwWebTimeLabel : WebControl
    {
        [Description("The text for the label displayed before the time value."), DefaultValue("Time: "),
         Category("Timer")]
        public string Text
        {
            get { return _Text; }
            set { _Text = value; }
        }
        private string _Text = "Time: ";


        [Description("Determines how often the time value refreshes"), DefaultValue(1000),
         Category("Timer")]
        public int Interval
        {
            get { return _Interval; }
            set { _Interval = value; }
        }
        private int _Interval = 1000;


        [Description("The message displayed in front of the time value"), DefaultValue(DisplayModes.Time),
         Category("Timer")]
        public DisplayModes DisplayMode
        {
            get { return _DisplayMode; }
            set { _DisplayMode = value; }
        }
        private DisplayModes _DisplayMode = DisplayModes.Time;


        protected override void Render(HtmlTextWriter writer)
        {
            this.Text = this.Text + " " + TimeValue;

            base.Render(writer);

            string TimeValue = DateTime.Now.ToString("d");
            if (this.DisplayMode == DisplayModes.Time)
                TimeValue = "2 seconds";

            writer.Write("<span id='" + this.ID + "' class='" + this.CssClass +
                         "' style='color:" + this.ForeColor.ToString() +
                         ";background:" + this.BackColor.ToString() + ";'" +
                         ">" + this.Text + " " + TimeValue + "</span>");

        }


        #region * ** Overriden hidden properties
        [Browsable(false)]
        public override string SkinID
        {
            get
            {
                return base.SkinID;
            }
            set
            {
                base.SkinID = value;
            }
        }

        [Browsable(false)]
        public override bool EnableTheming
        {
            get { return _EnableTheming; }
            set { _EnableTheming = value; }
        }
        private bool _EnableTheming = false;


        [Browsable(false)]
        public override Color BorderColor
        {
            get
            {
                return base.BorderColor;
            }
            set
            {
                base.BorderColor = value;
            }
        }

        [Browsable(false)]
        public override short TabIndex
        {
            get
            {
                return base.TabIndex;
            }
            set
            {
                base.TabIndex = value;
            }
        }

        [Browsable(false)]
        public override BorderStyle BorderStyle
        {
            get
            {
                return base.BorderStyle;
            }
            set
            {
                base.BorderStyle = value;
            }
        }

        [Browsable(false)]
        public override Unit BorderWidth
        {
            get
            {
                return base.BorderWidth;
            }
            set
            {
                base.BorderWidth = value;
            }
        }
        #endregion


    }

    public enum DisplayModes
    {
        Time,
        Seconds
    }
}
```