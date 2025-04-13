This topic briefly describes driver configuration for several of the tools provided. There's also additional information available in the source code headers for each class in the wwPDF.prg file that contains all of these classes.

* [Generic PDF Printer Driver and wwPdfPrintDriver](VFPS://Topic/_5AS0URRCN)
* [GhostScript and wwGhostScript](vfps://Topic/_1H40V1USY)
* [XFRX and wwXFRX](vfps://Topic/_1H40V3TE3)
* [Adobe Distiller and wwDistiller](vfps://Topic/_1H40V11WH)
* [AmyUni and wwAmyUni](vfps://Topic/_1H40V5OBH)
* [ActivePdf and wwActivePdf](vfps://Topic/_1H40VHOJY)

Web Connection provides a group of wwPDF classes each of which implements one of the specific drivers or engines. The classes are functionally compatible and the main value of these classes is to provide easy, interchangeable pluggability.

> ##### Performance Consideration: Print Spooler is Limited
> Any print solution that goes through the Windows Print Spooler is not going to scale very well as the print spooler can only accept a single print job at a time. This means it can be slow, but also can mean that the spooler may reject print jobs when too many are sent.
>
>For high volume applications the only solution that lends itself to better scalability is the **wwXFRX driver** using the <a href="http://eqeus.com/" target="top">XFRX third party library</a> as it doesn't use the Windows print spooler but rather captures printer output directly within the context of the active application.

### Switching Printer Drivers
wwPDF provides a common interface to the various drivers so it's easy to switch between them.

To faciliate this process we recommend that you **never hardcode the name of the class** but rather use a #DEFINE. Web Connection (in wconnect.h) defines:

```foxpro
#DEFINE WWC_WWPDF				   wwPdfPrinterDriver
```

You can override this to match your class in WCONNECT_OVERRIDE.H (or whereever you choose if it's not a Web Connection application). The override looks like this:

```foxpro
#UNDEFINE WWC_WWPDF  
#Define WWC_WWPDF         wwPdfPrinterDriver
* #DEFINE WWC_WWPDF                  wwXFRX
* #DEFINE WWC_WWPDF               	wwGhostScript
* #DEFINE WWC_WWPDF               	wwDistiller
```

In addition to specifying the wwPDF class you'll also need to specify the specific printer driver for any solution (all but XFRX really) that rely on printing output through Windows using 


```foxpro
*** WWC_WWPDF is defined in WCONNECT.H
*** or you can override it here 
*** wwMicrosoftPdf, wwGhostScript, wwXFRX, wwAmyUni
oPDF=CREATEOBJECT([WWC_WWPDF])     

*** Specify which Windows Printer NAME to use
oPdf.cPrinterDriver = "Microsoft Print to PDF"
	
*** Display in browser (potential browser issues with older Adobe viewers)
IF lnMode = 0 
   lcPDF = oPDF.PrintReportToString("custlist.frx")
   Response.ContentType = "application/pdf"
   Response.Write(lcPDF)
ENDIF
*** Download the file (more reliable)
IF lnMode = 2 
   *** Temporary filename
   lcFile = SYS(2015) + ".pdf"

   oPDF.PrintReport("custlist.frx",Server.oConfig.owwDemo.cHTMLPagePath + "temp\"+lcFile)
   Response.DownloadFile(Server.oConfig.owwDemo.cHTMLPagePath + "temp\"+lcFile,;
                         "application/pdf",;
                         "CustomerReport.pdf")  
ENDIF
```

### Installing a specific printer Driver
Web Connection includes a few ways to install printer drivers more easily:

* Use the Web Connection Menu, Tools -> Install PostScript Printer Driver
* Use `InstallPrinterDriver()` function in wwAPI
* Use the `Console.exe` command line

All of these tool use the same `InstallPrinterDriver()` function, using different front ends. You can specify any **installed** Windows printer using this method. For the following examples I'll use the `Microsoft Print to PDF` driver.

To install you can use the following code from the command window from FoxPro:

```foxpro
DO wwapi
InstallPrinterDriver("Microsoft Print To PDF")
```

Or you can use the Console from the Windows command line:

```ps
Console INSTALLPRINTER "Microsoft Print To PDF"
```