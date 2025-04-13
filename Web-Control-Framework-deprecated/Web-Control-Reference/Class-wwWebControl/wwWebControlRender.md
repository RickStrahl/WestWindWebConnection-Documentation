The core method for each Web Control class that is responsible for creating the final HTML output for the control.

The Render method should check for several things:
* Visibility - if invisible just exit
* Cacheability - is the control content cached?
* Call into ChildControls
* Write out PreHtml and PostHtml