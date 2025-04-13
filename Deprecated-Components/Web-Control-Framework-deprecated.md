The Web Connection WebControl Framework is a rich, object oriented Web Development framework similar to ASP.NET that lives ontop of the core Web Connection engine. Although there are similiarities to ASP.NET and you can use Visual Studio 2005/Visual Web Developer to create your HTML Script pages, it is based 100% on FoxPro code.

The framework uses a control based architecture that lets you access Web content through controls with properties rather than dealing with low level HTML elements directly (although that is still completely possible). In additon an event based model makes it much easier to write isolated code for specific actions instead of monolithic methods that need to handle lots of different operations. A Web Page button click can be mapped to a FoxPro method in a Page class for example.

The framework supports visual editing support in Visual Studio .NET 2005/Visual Web Developer 2005 including using property editors. An included script parser can use script pages created in VS.NET and turn them into a FoxPro class that can be executed in Visual Foxpro. Your code then implements another class that provides the base for this generated class and lets you create you FoxPro business application logic.

The Page Pipeline has the following advantages over traditional Web Connection Applications:

* **Rich Control based Architecture**  
Using the framework you program against controls, rather than creating HTML markup on your own. You can use Visual Studio's rich design time environment to drag and drop controls and assign properties in the Property Grid and get Intellisense in HTML View. This architecture reduces the amount of time it takes to build rich applications drastically. And it's a closer match to the traditional Desktop Forms programming model.

* **Remembers Page State**  
Each control on the page automatically maintains post back data when you Post back a page. No more using the Request.Form() method to retrieve data. Instead each control automatically assigns the data back to itself.

* **Can deal with persistence**  
The page can manage control state persistence in situations where it normally doesn't work such as posting back from disabled controls (which don't post back!) and for controls like checkboxes which have non-determinate state conditions. ViewState allows storing of page specific content in the page without hidden form variables. Internal properties can be persisted into ViewState automatically with the PersistProperty() method of a control persisting state across PostBacks. All of this makes it much easier to build complex pages that contain multiple logical elements.

* **Two-way ControlSource Databinding**  
The page framework allows two-way databinding for simple controls of controls to values/expressions. You can set a ControlSource property and the framework will - with a couple of simple method calls to either DataBind() or UnbindData() - bind and unbind the data to and from the ControlSource respectively. If you've used VFP's form ControlSource binding you already know how this mechanism works.

* **Provide Automatic Error Handling**  
The page framework can automatically capture binding errors and unbinding errors. For unbinding, errors are stored in a BindingErrors collection that can be easily persisted into HTML. Event better it works together with WebErrorDisplay object to display these errors in a rich fashion with error icons, control highlighting, links to errors and more. The error handling can also be handled manually by creating binding errors from code - for example, for business rule validation errors. wwBusiness has been enhanced with an oValidationErrors property that can be directly loaded into the Page.BindingErrors collection so you can display both databinding and business logic errors in one place. 

* **Can fire Events**  
Controls can implement events that are captured and fired against your FoxPro code on the server. Basically you implement a method on a control and from the server side control tell the control to trigger this event. The most common events are Click on a button or link, or Change on things like listboxes, both of which are automatically handled. With script tags this looks like this:

```html
<ww:wwWebButton runat="server" id="btnSubmit" Text="Go" Click="btnSubmit_Click"/>
```

All you need to do to handle this event then is implement a FoxPro based btnSubmit_Click method on the Page class of the server where all your implementation code is written:

```vbnet
FUNCTION btnSubmit_Click(loCtl)
this.lblMessage.Text = "Hello " + this.txtName.Text + ;
                       ". Time is: " + TRANS(DateTime())
ENDFUNC
```

It's hard to understand the utility of this mechanism and the affect it has on manageability readability of your code until you have a chance to use it and see how little code you end up writing in your event methods to manage the Web Interface.

* **Provides WYSIWYG editing in any ASP.NET based editing environment**  
You can create your script pages in VS.NET 2005 or Visual Web Developer or any editor that supports ASP.NET 2.0. You create your script pages in a rich editor incuding the designer and property sheets, and the WebPageParser class can then automatically turn your script page into a FoxPro class. This process is automated as part of the Web Connection server so when you run your code the page is automatically parsed, compiled and executed detecting any changes immediately.

* **Completely extensible**  
Because Controls are merely classes you can easily extend the framework by creating new controls or extending existing ones. The controls are accessible simply by classname. This offers tremendous opportunities to provide add-on functionality to Web Connection.