The AmyUni product is a printer interface and PDF manipulation tool which acts as a printer driver and allows direct printing to PDF. AmyUni was one of the original printer driver companies and their licensing prices are reasonable (although difficult to figure out). They provide a FoxPro FLL interface to direct printer output to the AmyUni printer driver. AmyUni is a good choice for an economical legal licensed server implementation.

You need:
<ul>
* Install the AmyUni Software
* Copy the cdIntf.dll and fllIntF.fll files into the Fox path of your application
* Set the cPrinterDriver property to the AmyUni dynamic printer.
* After installation check for the exact name of the installed drivers and
use that in the cPrinterDriver property
</ul>

Example:
```foxpro
oPDF = CREATEOBJECT("wwPDFAmyUni")
oPDF.cPrinterDriver = "PDFPrinter"  
oPDF.PrintReport("customer.frx","d:\temp\customer.pdf")
```


<a href="http://www.amyuni.com/pdfpd.htm#pdfconvdesc" target="top">AmyUni Web site</a>
**Price:** ($129 single license)