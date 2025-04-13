The Web Control framework comes with a number of stock controls and this section is a reference to these controls.

Because there are so many controls this list is broken up into Core Controls which are the base class controls and some elemental controls like WebPage and WebUserControl. And then there are the following subgroups:

<ul>
* [Base Controls](vfps://Topic/_1MT0THRY9)
* [List Controls](vfps://Topic/_1MT0TI7CE)
* [Extended Controls](vfps://Topic/_1MT0TIM03)
* [Expression Evaluation](vfps://Topic/_1OO1EOQ75)
</ul>

### Control Inheritance
This guide shows properties, events and methods that each control implements. All controls inherit from wwWebControl, so all controls will inherit all the properties and methods of this base class at the least. So keep in mind that as you look at the documentation for each control that what you see for the specific control will be augmented by the features of wwWebControl or any other control class that is inherited from (list controls like wwWebListBox inherit from wwWebListControl which in turn inherits from wwWebControl for example).

### Base Controls
The following is a list base controls that drive the base functionality of the Web Control framework. wwWebControl is the base control for all other controls, while wwWebPage is the entry point for Page processing that manages postback and viewstate management as well as event processing. The wwWebUserControl is a visual control class that allows creation of composite classes by users and embed them into other pages. 

<%= ChildTopicsTableHtml(oHelp,"CLASS","Class",,"" ) %>