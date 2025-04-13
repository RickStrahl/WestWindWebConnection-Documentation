Web Controls also support events. In script code events are mapped by using an attribute for the event name and then assigning a handler method on the form to it.

```html
<ww:wwWebButton runat="server" id="btnSubmit" Text="Click me" 
     Click="btnSubmit_Click" />
```

The above maps the button's Click event to the btnSubmit_Click method on the WebPage which should look like this:

```foxpro
FUNCTION btnSubmit_Click()
	* ** Do something
	this.btnSubmit.Text = "Clicked"
ENDFUNC
```

In code this maps to the following using the HookupEvent method of a control:

```foxpro
THIS.btnSubmit = CREATEOBJECT("wwwebbutton",THIS)
THIS.btnSubmit.Id = "btnSubmit"
THIS.btnSubmit.Attributes.Add("AccessKey","S")
**THIS.btnSubmit.HookupEvent("Click",THIS,"btnSubmit_Click")**  
THIS.btnSubmit.Text = [ Save Topic ]
THIS.btnSubmit.Width = [131px]
THIS.AddControl(THIS.btnSubmit)
```

If you hook up an event in the script page or via code and don't have a corresponding handler method defined in your class, you will get a runtime error immediately when the page runs as a reminder that you have to implement the method. The error occurs inside of the Web Connection framework in the HookupEvent method when debugging is on, otherwise it bubbles to the page's OnError handler or if not handled there to the Process.OnError method.

### Event Firing from the Client
Note that you can map any function in this manner. But keep in mind that it's the control's responsibility to fire these events and make them happen. Some events like Button Clicks are automatic, but most other events like an OnChange event or PageChanged event needs to be explicitly triggered by the client code.

Events can be initiated on the client via the PostBack script handler. This script handler is a small bit of JavaScript that initiates a POSTBack, and passes back events to the server. The server parses the event parameters and fires events based on that. 

It's usually the responsibility of the server control to:
<ul>
* Make sure the Postback script code gets included in the page
* Create the appropriate PostBack trigger code to trigger an event
</ul>

**Example: DataGrid Paging Events**  
This is easiest to explain with an example. The wwWebDataGrid includes a PageChanged event. In order to fire this event when you click on one of the paging buttons you'll see code like this fired from the button:

**javascript:__doPostBack('gdEntries','PageIndexChanged','2')**  

The control actually generates these script calls into the HTML:

```html
<tr class="gridpager" ><td colspan="2" align="right" class="gridpager" >
Pages: <b>1</b> 
<a href="javascript:__doPostBack('gdEntries','PageIndexChanged','2')" style="color:white">2</a> 
<a href="javascript:__doPostBack('gdEntries','PageIndexChanged','3')" style="color:white">3</a> 
</td></tr></table>
```

These events specify to call the PageIndexChanged event on the gdEntries control with a parameter of 2 or 3 or whatever. This actually triggers an event on the server.

The script call above includes the __doPostBack() script which looks like this:
```html
<input type="hidden" id="__EVENTTARGET" name="__EVENTTARGET" value="" />
<input type="hidden" id="__EVENTARGUMENT" name="__EVENTARGUMENT" value="" />
<input type="hidden" id="__EVENTPARAMETER" name="__EVENTPARAMETER" value="" />

<script type="text/javascript">
var theForm = document.forms['form1'];
if (!theForm) {
    theForm = document.form1;
}
function __doPostBack(eventTarget, eventMethod,eventParameter) {
    if (!theForm.onsubmit || (theForm.onsubmit() != false)) {
        theForm.__EVENTTARGET.value = eventTarget;
        if (eventMethod)
           theForm.__EVENTARGUMENT.value = eventMethod;
        if (eventParameter)
            theForm.__EVENTPARAMETER.value = eventParameter;
        theForm.submit();
    }
}

</script>
```

So how does all of this get generated? The Web Control needs to manage this on its own. So the wwWebDataGrid does the following.

In OnPreRender() it checks to see what the PageSize is. If it's greater than 0 it needs to add the post back script and it also need to trigger keeping track of the current page selected in ViewState:

```foxpro
* wwWebDataGrid::OnPreRender()
FUNCTION OnPreRender()

IF THIS.PageSize > 0 AND !ISNULL(this.Page)

   * ** Must register stock code for Page changes
   this.Page.RegisterPostbackScriptcode()

   * ** We want to automatically save the CurrentPage Index
   this.PreserveProperty("CurrentPageIndex")
ENDIF
IF !EMPTY(this.SortColumn)
   this.Page.RegisterPostbackScriptcode()

   * ** Always persist the sort column if it's set
   this.PreserveProperty("SortColumn")
ENDIF
IF !EMPTY(this.SortOrder)
   this.PreserveProperty("SortOrder")
ENDIF
  
ENDFUNC
```

This ensures that the Postback script gets injected into the form. Note that if the script exists already it's not added more than once.

To render the script calls that trigger the PageIndexChanged event in each of the pager links is the responsibility of the control which manages this as part of the rendering process:

```foxpro
PROTECTED FUNCTION PagerPostBackLink(lnPage,lcText)
IF EMPTY(lcText)
   lcText = TRANSFORM(lnPage)
ENDIF   
RETURN  [<a href="javascript:__doPostBack('] + this.UniqueId + [','PageIndexChanged','] + ;
        TRANSFORM(lnPage) +[')" style="color:] + this.PagerTextColor + [">] + lcText + [</a>]
```

The logic to decide which buttons to display is actually a bit length to show here, but just know that it loops through all the pages that are to be displayed and calls this method to actually inject the link into the page.

### Firing the event
Once the user clicks on one of the pagers the event is officially fired. The event values are encoded into hidden form variables which are sent to the server. The are:

<ul>
* **__EVENTTARGET**  
The object that the event is fired on. This is a control's UniqueId value.

* **__EVENTARGUMENT**  
The method or event that is fired on the control. If not provided the default event for the control is fired (ie. Click on a button, OnChange on a Text box etc.)

* **__EVENTPARAMETER**  
An optional parameter value that is user defined. This value can be used by the control to figure out some sort of state that is passed back from the client.
</ul>

These parameters are then used by the WebPage's FireEvents method to determine which control and which event is to be fired if any. The WebPage then calls the control's method that maps the event specified. So if the event is click on btnSubmit the btnSubmit.Click method is fired.

btnSubmit.Click() however is not going to have any code in it because it's on the base control. So rather you have attached an event handler to this 'event' using btnSubmit.HookupEvent() which is automatically generated for you when you use the script syntax described earlier:

```html
<ww:wwWebButton ... Click="btnSubmit_Click" />
```

HookupEvent routes the event to your specified method - in this case the WebPage control's btnSubmit_Click method, where you can then handle the event.

### Events are a modular way to code!
The event handling mechanism is very powerful and at the core of the Web Connection WebControl Framework. Compared to classic Web Connection applications it allows isolation of distinct blocks of code into logical sippets much in the way you write code in desktop applications. This makes for more modular and much easier management of code in complex pages. Rather than one monumental function that has lots of CASE statements to figure out where to go you have event methods that logically map to operations from a Web page.