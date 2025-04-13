This is a standalone routine that causes the page to process the Event pipeline and write output into the Response object. This method handles setup and cleanup plus error handling for the page.

This method is very highlevel and acts merely as a controller wrapper around the wwWebControl::Process method. The Run method basically does following:
<ul>
* Sets up an error handling construct (depending on DebugMode setting)
* Calls Page.Process() to fire the event pipeline
* Calls the Page.Render() method to cause the page and components to generate output
* Calls Page.Dispose() to clean up the Page and its components
</ul>
This method is typically called for you as part of the Page class PRG file which contains a stub that does the following:

```foxpro
#INCLUDE WCONNECT.H
* ** Small Stub Code to execute the generated page
PRIVATE __WEBPAGE
__WEBPAGE = CREATEOBJECT("PunchIn_Page_WCSX")
__WEBPAGE.Run()
RELEASE __WEBPAGE
RETURN
```

so you can just DO <YourPage>.prg to 'execute the actual page.