The page event sequence is crucial to understanding how the WebControl Framework works. A request runs through the entire pipeline started by the wwWebPage::Run method which kicks off the whole process of events. The Run method fires the events shown in the figure below on the Page object, which in turn delegates these events to any ChildControls of the Page. Any container object in turn delegates the events into their ChildControls in turn.

Any of these events can be intercepted either by subclassing the control or Web Page or by using BindEvent() to attach to the event method. The most common implementation is in your custom Page implementations where you create your application level code.

The following figure shows the high level event sequence:

![](IMAGES%5CWebControls%5CHowItWorks%5CPageEventSequence.jpg)

Although the image above shows the wwWebForm event sequence, this sequence really applies to all controls as Parent controls fire these same events on each one of their ChildControls. 

* **OnInit()**  
Fired on Initialization of the control. The control should make no assumptions about other controls or the Page at this time. The firing order is controls fire first, the container last.This method is a good place to add new control via code or modify startup properties of the currently executing control.

* **OnLoadViewState()**  
Called immediately after Init is complete and loads up control state from ViewState. ViewState is a special state mechanism that is written into a hidden form variable and posted back when the page is returned and it allows tracking of values that don't POST back. For example, it can help track things like button captions and colors etc. Viewstate comes in two flavors: Implicit and Explicit. Implicit is used by the various controls to persist themselves as needed. For example, the DataGrid always persists the current PageIndex so it can be picked up when the page returns. Textboxes persist their text IF a control is disabled or invisible. Explicit Viewstate lets you persist specific values by either using the Control.PersistProperty() method or by using the ViewState collection to add values to ViewState manually. ViewState loads up controls state before POST data is applied - IOW, POST data overrides any values set in Viewstate.

* **OnLoadControlState()**  
Called right after ViewState aquisition. This method loads control values into their DefaultProperty value from POST data. This means TextBox Text, CheckBox Checked, List SelectedValue  values are read from the POST buffer and assigned providing your application with controls value access to Form data without writing any Request code.

* **OnLoad()**  
Fired after the control has completely loaded. At this time the state of the control should be stable and the state of the control set from ViewState or POST buffer. OnLoad is the key method for doing 'business work' on a control. Page.OnLoad() is the method that is used to do most of your non-event page processing and all of your processing in read-only pages.

* **FireEvents()**  
Once OnLoad has fired FireEvents() is called which causes any events fired from the client to be routed to the appropriate event handler. So, if a button click event is configured the Page's OnLoad() method fires first, followed by the Event method configured for the button. Events are wired via BINDEVENT in VFP and bind to event method configured on the base WebControl method. 

* **OnPreRender()**  
OnPreRender() is a last chance hook that is used after supposedly all processing is complete on the page, but just before the page starts rendering. This method is useful to check flags to see whether some task was completed else where and if not take final action to configure the output. It's also a good hook to create State Machine like formatting of control properties.

For example, you may have a form with a grid on it, but there are many controls that determine how that grid renders. So you might hook a ShowGrid method from the PreRender() method that is responsible for setting all the grid properties and Databinding the grid at the last minute with all the settings that were previously made. Usually this approach is much better than applying specific Databinding in events or OnLoad as it often results in multiple databinding calls.

* **Render()**  
Render() is responsible for generating the final output for the control. For most controls this method does most of the work and pulls together all the pieces to render the final output - usually HTML. Render() manages many aspects of the control including dealing with visibility (basically not rendering if invisible), DisplayError display and managing PreHtml and PostHtml. Control designers should look carefully at implementations of the base controls to see what is required.

* **OnSaveViewState()**  
The last step on the pipeline is to save any Viewstate that has been set by the page or simply is forwarded from the previous page hit. Each control again manages this on its own and the Form takes all the control's viewstate values and combines them into a single value that gets wrapped up and written into a hidden form variable in the HTML document. 

Note: Unlike ASP.NET Web Connection's use of Viewstate is very lightweight as it doesn't persist things like list data content. Rather it persists only crucuial state values and values like that of disabled controls which is required. You can persist those kinds of values if you choose but you have to do it explicitly.

* **OnError()**  
Every page also gets an OnError event fired in the event of an error. You can choose to handle the error on the page level or let it bubble back up to the wwPageProcess class to handle.

* **Dispose()**  
This method isn't really an event but it's fired off the Destroy. Dispose() is very crucial to the framework in that it makes sure that all child controls and dependencies are destroyed correctly. Control developers need to make 100% sure that Dispose() releases all references to other controls explicitly! This includes references to parent controls and any child controls. Dispose should always call the Dispose() method of any child controls before performing its own cleanup code.