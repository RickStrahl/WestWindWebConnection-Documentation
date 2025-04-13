Performs databinding on a specific control. The base implementation is an abstract method so you need to provide an implementation for each control you create.

Databinding comes in two flavors:
<ul>
* **Single Value/ControlSource Binding**  
Simple binding is used on single field controls like TextBoxes, Labels, Checkboxes etc. and is implemented via the ControlSource property. The ControlSource binds to a specific field specified in the ControlSourceProperty - usually this will be the Text property but it can also be something else. For example, a Checkbox binds to Checked.

Controlsource binding occurs either by individually calling the control's DataBind() event, or by calling the WebPage's DataBind event which calls into each control's DataBind event in turn. ControlSources can also be unbound - which is the reverse process of taking the control's value and putting it back into the underlying field or property. You use the Unbind method on the control or the WebPage for this task.

* **List Binding**  
List Binding is used for List controls like the DropDownList, Listbox and DataGrid. Listbinding is automatic and occurs when the control is rendered. There are some exceptions - for the DataGrid for example DataBind is required for Automatic Field Generation. Note that some list controls like the DropDown and ListBox also support ControlSource binding for their selected values.
</ul>

If you're new to DataBinding please check out the databinding section of the documentation as there are a few important considerations you need to think about.