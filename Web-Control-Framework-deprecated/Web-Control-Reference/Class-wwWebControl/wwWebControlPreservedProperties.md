A collection of properties of the control that are persisted into ViewState. 

By specifying a propertyname you are telling Web Connection to save the property value into viewstate and retrieve it again on the next Postback. This is a very powerful feature that allows keeping state for properties that don't normally post back. For example, if you want to track a button caption or the color of a label.

Note that PreserveProperty must be reset on every hit, so a call to PreserveProperty only affects the immediate request. If you want to more permanently track properties add them at the beginning of the Onload() override of your form.