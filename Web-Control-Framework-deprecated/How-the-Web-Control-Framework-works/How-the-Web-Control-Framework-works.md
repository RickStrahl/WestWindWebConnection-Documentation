The Web Connection WebControl Framework is a powerful abstraction layer for creating Web based content. It brings a control based architecture to Web development which removes to a large degree the low level requirements for accessing Web content, and instead allows interacting with more familiar user interface objects such as textboxes, labels, listboxes and so on.

The architecture is fairly large under the covers, but it follows a relatively simple programming paradigm that's based on Inheritance, Containership, Delegation and Eventing.

In conversational terms the idea of the WebControl framework is this:
* There's a master Page object
* It contains other controls like TextBoxes, ListBoxes, Lables etc. 
* Some controls can contain yet other Controls (for example a wwWebPanel)
* The Page fires a series of operational events
* The events are delegated to each of the child controls
* Each control handles each event and performs it's own state operations
* Each control is self contained and responsible for its own output
* One of the events causes each control to Render() itself
* The rendered output is captured and combined in the right order
* The output is assembled into the final HTML output
* The output goes back to the Web Server

Although a very simple view of the Page framework it describes accurately the process involved. The key to understanding the framework is:
* [The Control Architecture](vfps://Topic/_1LO01TAK6)
* [The Page Event Sequence](vfps://Topic/_1MB01UF4D)


### Page Pipeline for automatic Page Processing
The Page Arcititecture provides the basic inheritance and containership model. All Controls derive from a common wwWebControl class which provides a lot of the core behavior for controls. All other classes are derived from this class. Controls can either be simple controls or containers. Containers have the ability to contain child controls. The parent controls send event messages to the child controls to perform processing.

Each control is a self contained unit and responsible for its own state (with a few exceptions like Viewstate which is stored on the form). Events fire on the page which is the highest level container. Page then delegates the events down to each control in turn. The key method where most controls do their core processing is the Render() method that is responsible for generating HTML output for the control.

Render() is but one of the 'events' that are fired in specific order for each control that exists on a Page. Each control supports additional events that can either be manually fired - or more commonly - get fired from a Page object and its Run() method. The Run() method fires a sequence of events against all the controls contained on it.

The Page Pipeline has the following advantages over traditional Web Connection Applications:

* **Remembers Page State**  
Each control on the page automatically maintains post back data when you Post back a page. No more using the Request.Form() method to retrieve data instead each control automatically assigns the data back to itself.

* **Can deal with persistence**  
The page can manage control state persistence in situations where it normally doesn't work such as posting back from disabled controls (which don't post back!) and for controls like checkboxes which have non-determinate state conditions.

* **Can perform two-way DataBinding**  
The page framework allows two-way databinding for simple controls of controls to values/expressions. You can set a ControlSource property and the framework will - with a couple of simple method calls to either DataBind() or UnbindData() - bind and unbind the data to and from the ControlSource respectively. 

* **Provide Automatic Error Handling**  
The page framework can automatically capture binding errors and unbinding errors. For unbinding, errors are stored in a BindingErrors collection that can be easily persisted into HTML. Event better it works together with WebErrorDisplay object to display these errors in a rich fashion with error icons, control highlighting, links to errors and more. The error handling can also be handled manually by creating binding errors from code - for example, for business rule validation errors. wwBusiness has been enhanced with an oValidationErrors property that can be directly loaded into the Page.BindingErrors collection so you can display both databinding and business logic errors in one place. 

* **Can fire Events**  
Controls can implement events that are captured and fired against your FoxPro code on the server. Basically you implement a method on a control and from the server side control tell the control to trigger this event. The most common events are Click on a button or link, or Change on things like listboxes, both of which are automatically handled. With script tags this looks like this:

```html
<ww:wwWebButton runat="server" id="btnSubmit" Text="Go" Click="btnSubmit_Click"/>
```

All you need to do to handle this event then is implement a FoxPro based btnSubmit_Click method on the Page class of the server where all your implementation code is written:

```foxpro
FUNCTION btnSubmit_Click(loCtl)
this.lblMessage.Text = "Hello " + this.txtName.Text + ;
                       ". Time is: " + TRANS(DateTime())
ENDFUNC
```


* **Completely extensible**  
Because Controls are merely classes you can easily extend the framework by creating new controls or extending existing ones. The controls are accessible simply by classname. This offers tremendous opportunities to provide add-on functionality to Web Connection.