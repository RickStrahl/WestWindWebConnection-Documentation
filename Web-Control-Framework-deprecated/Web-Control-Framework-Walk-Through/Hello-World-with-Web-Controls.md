Up to this point we've added controls to the page, but we haven't updated the page nor written any code. That's about to change. Let's take the page we created in the last topic and lets add logic to do the obligatory Hello World code.

Let's start by handling the Click event of the button. To do this go to Design View and select the button then enter "btnSubmit_Click" into the Click property of the property sheet in the Web Connection Events section. 

Alternately you can make the change in code:

```html
<ww:wwWebButton ID="btnSubmit" runat="server" Text="Go" Width="80" Click="btnSubmit_Click" />
```               

Notice the Click="btnSubmit_Click". What we're telling Web Connection to do here is to route the Button's Click event to the btnSubmit_Click method of the CodeBehind Page class.

Now switch to VFP and open the Helloworld_Page.prg file. Change the Helloworld_Page class by adding the following method to it:

```foxpro
FUNCTION btnSubmit_Click()
this.lblMessage.Text = "Hello " + this.txtName.Text + ". Time is: " + TRANSFORM(DATETIME())
ENDFUNC
```

Now go ahead and run the form. You should now see the message in the label control underneath the Textbox with the caption changing with the name of the input field and the changing Time value.

The button click fires an event into your FoxPro class and the method specified in the Click property. The method is called and you can write any FoxPro code in this method you see fit. 

The beauty here is that you are talking to an object with properties and for the most part the details of the HTML generation are hidden from you. The framework does the right thing rendering the control into the appropriate HTML. 

On closer inspection you also see another nice feature of the page framework: Notice that the textbox automatically maintained its input value on the button click. If you're new to Web Development this may not seem like a big deal, but if you've done Web Development with most non ASP.NET like tools you had to manually repopulate the values into textboxes by explicitly binding them - typically with <%= Expression %> sytnax. It's automatic with the Web Control framework which automatically maps the POST data back into the appropriate control or controls.

### Saving non-POSTback values
The above is nice, but it works with a value that's posted back. What if we want to change a property that doesn't get posted back? For example add another textbox and button to the form as txtColor and btnColor like this:

```html
Enter a color:
<ww:wwWebTextBox ID="txtColor" runat="server" Width="165px">Orange</ww:wwWebTextBox>
<ww:wwWebButton ID="btnColor" runat="server" Text="Change Color" Width="114px" Click="btnColor_Click"/>
```

The form should now look like this in the designer:

![](/images/WebControls/FirstControls2.png)

Then hook up this code in the FoxPro class:

```foxpro
FUNCTION btnColor_Click()
this.btnColor.ForeColor = this.txtColor.Text
this.btnColor.Style = "font-weight:bold"
ENDFUNC
```

Run the page again and refresh. You'll notice clicking the button indeed changes the color and font weight of the button as expected. So here is another example of an assignment to a page control.

But you may also notice that there is a potential problem with the demo now if you click back and forth between the Say Hello and Cange Color buttons. If you click on Say Hello after changing the color of the button you loose that color. If you click on the color  button after the Say Hello button you lose the label text. 

Now this behavior may actually what you want. A value set on a label may or may not be permanent in a Web application, same with a color. The behavior works as it does because a Web page is stateless and any non-POST values are not restored automatically. POST values are textboxes, checkboxes, list values, radio buttons etc. all of which POST back to the server when the page is submitted. The values are sent and Web Connection can restore the state.

However, properties like captions, colors, attributes, styles etc. are not posted back and so can't be automatically restored. The next time the page loads these values revert back to their default values. For the Label it means blank. For the button it means no color and non-bold.

This is normal for Web applications and probably appropriate for most scenarios. However if you do want to persist the value of a propery you can do this by adding the following to the OnLoad() of the Page:

```foxpro
FUNCTION OnLoad()
this.btnColor.PreserveProperty("ForeColor")
this.lblMessage.PreserveProperty("Text")
ENDFUNC
```

Now run the demo again and you'll see that both the text and the color are indeed preserved.  Note that the style of the button is not preserved so the button turns Bold on a click, but loses the bold when you click Say Hello again. That's because we didn't persist the style. If you wanted to keep the style as well:

```foxpro
this.btnColor.PreserveProperty("Style")
```

and voila the boldness gets persisted as well.

This is a powerful concept as it allows you to persist data between postbacks. PreserveProperty works with Controls and the Page itself too. So you can store properties of the Page object. Note that this Method works only with simple types - objects are not supported.

What's happening behind the scenes is that these properties get written into ViewState. ViewState is an encoded string of all the preserved state of all the controls. This encoded string is persisted into the WebPage and posted back for each request. The control framework knows how encode and decode the ViewState and repopulate properties whenever a page is posted back. Web Connection picks up the Viewstate and reassigns it to the controls when they load the next time, which effectively persists these non-conventional values.

So why is this not automatic? It sure would be nice if you could set a property and it would automatically preserve. The problem is two fold. First you may not want to persist values all the time. For example, you may be setting the lblMessage to some rather large value and if you persist it in ViewState that value keeps moving back and forth. You may not need that. So ViewState size is one issue. Web Connection only persists a minimal amount of settings in Viewstate. The other issue is figuring out what's changed. VFP doesn't provide a good mechanism at runtime to see whether a property still contains its default value, so there's really no good way to know what to persist. You'd have to manually parse control properties which would be extremely slow. The other alternative is to persist certain properties only, but even that is a very expensive proposition in processing time. In the end, we decided in the interest of performance it's best to not do any default persistence except for vital properties (for example, CurrentPageIndex on the WebDataGrid). It's easy enough to enable this functionality in initialization code. 

So any preservation of properties is declarative, but it's really easy to do with PreserveProperty() calls.

### What happens on Errors?
What if you made an error in your code? If it worked go back and put some garbage into the above click method and run the request again. Here's what you should see:

![](/images/WebControls/FoxCodeError_1.png)

The error stops right in your FoxPro code. Cool - you can fix the problem, go back to the command window start right back up. The code stops when the Server.lDebugMode flag is set to .T. which is on by default. You can change this flag in the application's startup INI file (DevDemo.ini - DebugMode flag) or on the Web Connection Status form. If lDebugMode = .F. the error displays in the browser like this instead:

![](/images/WebControls/Html%20Error%20Page.png)

This default error page can be overridden by subclassing your Process class and overriding the OnError method. Alternately you can use BindEvent to route the OnError method to your own handler and override the logic there. To see the default logic look at wwWebPageProcess::OnError() in wwProcess.prg.

Ok, you've now seen the control basics of the Web Connection Web Control framework. It's time to move on to some more interesting examples that involve data.

### Stock .WCSX or custom .DP extensions?
Note in the previous examples I've been using the .DP extension for my pages which is a custom scriptmap we created for our process class. Note that if you were to rename the Helloworld.dp page to Helloworld.wcsx it would work exactly the same way.

.DP is a map to a custom Process Class, which is useful if you need to override Process class options or provide processing that applies to every single request (every .DP page for example). When I created these demos originally that support was not there yet, so the following demos still use the stock .WCSX extension. You can use either that or the .DP extension. In your own applications I recommend you *always use a custom scriptmap* to allow for maximum flexibility even if you don't need it when you start out - chances are you will need it later.

So, as we move forward think of mentally replacing .WCSX extension with your custom extension!

Next: [Creating a customer list page with a wwWebDataGrid](vfps://Topic/_1LY1F7J8W)