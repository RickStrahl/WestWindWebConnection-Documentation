Script Pages follow some rather strict rules in order for the Web Connection Page parser to be able to parse the pages. Although the Web Control framework closely follows the ASP.NET syntax, this doesn't mean that all ASP.NET controls are supported, nor that all properties or syntax options are available.

The following rules must be observed:
<ul>

* **Attributes must have quotes around them**  
Every attribute in a server tag must have either single or double quotes around them. Non-quoted attributes will be ignored.

* **Attributes cannot contain < > tags**  
Any attributes inside of a server tag cannot contain any angle brackets. If they do there will be misparsed content. if you need to assign angle bracket content, you can do so via FoxPro code from the Page class.

* **Only one Server Form**  
A script page should contain exactly one server form. The page can however contain multiple non-runat server forms.

* **User Controls can't nest other User Controls**  
Currently user controls can't host other user controls. The parsing allows only one level of user control due to file location issues. This is something that will be addressed in future builds.

</ul>