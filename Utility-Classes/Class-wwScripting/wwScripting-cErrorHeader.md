This property can be used to display the top of the error message page should a scripting error occur.

This text should include everything including the <html> tag and HTML text but not the closing document tags (</body></html>. wwScripting will append the error information table and close out the document.

A default header is provided so this is an optional property.

The easiest way to utilize this feature is to subclass and assign the property as needed:

```foxpro
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ***
DEFINE CLASS wwHelpScripting AS wwScripting
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ***

cErrorHeader = ;   
 [<html><head>] + ;
 [<LINK rel="stylesheet" type="text/css" href="templates/wwhelp.css">] +;
 [</head>] + CHR(13)+CHR(10) +;
 [<body topmargin=0 leftmargin=0>] + CHR(13) +;
 [<TABLE WIDTH=100% class="banner"><TD ALIGN=CENTER><b><font SIZE="4">Help Builder Scripting Error</FONT></b></TD></TABLE><p>]+;
 [<div style="margin-left:15pt;margin-right:25pt;"><img src="file:///] + SYS(5) + CURDIR() + [bmp\images\alerticon.gif] + ;
 [" align="left">An error occurred during the processing of the Help Builder script page.] + CHR(13) +;
 [Most likely there's an invalid script tag inside of this script page. Help Builder has opened]  + CHR(13) +;
 [the script for editing and has tried to locate the cursor on the source of the error.<p>] + CHR(13) +;
 [The following error information is available:<p></div>] + CHR(13)

ENDDEFINE
```