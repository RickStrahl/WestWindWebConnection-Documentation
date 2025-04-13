The Page.Header controls is a simple container control that references the <head> tag of a page.

The main purpose of this control is to allow adding of additional header content programmatically, especially for control level code.

The wwWebPageHeader control which renders the header also adds two overloaded methods that you may find useful:

<ul>
* **AddControl(loCtl | lcLiteral)**  
In addition to allowing adding of a control you can pass a string of literal text that is injected. Content is added to the bottom of the header

* **AddControlAtTop(loCtl | lcLiteral)**  
Allows adding a control or a literal string (as above) at the top of the header.
</ul>

The header is used internally extensively to add scripts to the page.