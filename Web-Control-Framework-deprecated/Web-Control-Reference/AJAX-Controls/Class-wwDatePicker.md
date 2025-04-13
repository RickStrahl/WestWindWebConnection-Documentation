A simple date picker control based on <a href="http://marcgrabanski.com/article/82/jQuery-UI-Datepicker-v3" target="top">jQuery UI Datepicker control from Marc Grabanski</a>. 

![](IMAGES%2FWEBCONTROLS%2FWWWEBDATEPICKER.PNG)

The actual jQuery control is a control extender, while wwWebDatePicker is provided as a server control that exposes the properties of the client control for easy dragging and dropping on the form with control properties that can be set through your FoxPro code. You can still utilize the full client API of the jQuery control to further customize the control for things like range selection and customization of for regions and localization.

The control can operate in three modes as a button, auto-popup (click or enter textbox) or as an image button which is the default.

The control also supports several key board shortcuts for the textbox only:

<ul>
* T - Today
* + and -  - One date forward and back.
* M and H - One month forward and back.
</ul>