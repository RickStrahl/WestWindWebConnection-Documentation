XFRX is a VFP based solution for printing VFP reports to PDF. This solution is fully self contained: There are no printer drivers to install - you just install XFRX and include a few support files and you're ready to go. XFRX is essentially a report imaging solution that allows you to output standard FoxPro reports as PDF,quite nice plain DHTML, a Word Doc and a few other formats. XFRX's focus is purely on capturing report output.

Performance is excellent. The real value of this solution is that it is truly self contained and doesn't require any additional configuration. It also bypasses the Windows printing  system and as such doesn't need any Admin requirements for configuration and logon-less printing. Licensing is also very reasonable. I highly recommend XFRX if you plan on integrating PDF from report printing.  Implemented via the wwXFRX class.

There are two classes:

* wwXFRX
* wwXFRX9

The latter uses the newer VFP9 report listener framework which provides slightly different internals.

**You need:**  

* Include the XFRX APP file in your path or include the source files in your project
* Include the support DLLs with your application path
* The following files from the XFRX install must be in your search path:
XFRXLIB.FLL, HNDLIB.DLL, ZLIB.DLL,  MSVCR71.DLL (for VFP 8 and prior)
* `SET PROCEDURE TO UtilityReportListener.prg ADDITIVE`  (for the XFRX9 class)


```foxpro
* ** Add XFRX to your path for the APP,DLL/FLL files
* ** Do this in your Application startup  (ie. YourServer::SetServerEnvironment)
DO PATH WITH "d:\wwapps\XFRX-VFP9"
...

oPDF = CREATEOBJECT("wwXFRX")
oPDF.PrintReport("customer.frx","d:\temp\customer.pdf")
```

The wwXFRX class includes additional properties that actually let you output to HTML,DOC,PDF,JPG,TIFF etc. that XFRX supports (if you bought their full version). The class wrapper all of the powerful functionality XFRX provides and XFRX certainly is easy enough to use without this wrapper, but the class provides plug in compatibility with the wwPDF series of classes so you can hotswap output engines.

Thanks to Web Connection MVP Keith Hackett who actually implemented this class.

[XFRX Web Site](http://www.eqeus.com/frx2wrd.php)

**Price:** $119 - $319