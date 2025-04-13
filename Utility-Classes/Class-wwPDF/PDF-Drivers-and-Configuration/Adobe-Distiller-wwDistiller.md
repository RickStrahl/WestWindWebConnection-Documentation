The official Adobe solution to creating PDF output. Acrobat includes both PDFWriter and Distiller. However, this solution has license issues - make sure you read Adobe's licensing agreement. The Distiller object works across versions and tends to be more reliable for PDF generation than PDFWriter and we recommend that use the wwDistiller class with Acrobat. 

In order to use wwDistiller you also need to install a PostScript printer driver.

Distiller works by converting PostScript files to PDF files. As such wwDistiller prints a report to a PostScript file first and then converts the file to a PDF file. This technique is reliable and recommended as the best way to use Adobe's Acrobat tool to generate PDFs.

**You need:**  
<ul>
* Install Adobe Acrobat and make sure you install Distiller with the install
* Install any PostScript PrinterDriver (you can use Xerox Phaser 1235 PS)
* Set the cPrinterDriver property to this printername
</ul>

**Example**:
```foxpro
oPDF = CREATEOBJECT("wwDistiller")
oPDF.cPrinterDriver = "Xerox Phaser 1235 PS"  
oPDF.PrintReport("customer.frx","d:\temp\customer.pdf")
```

<blockquote><small>**Printer Driver Name**  
You can either set the printer driver explicitly as in the example above or subclass and set the property in the subclass. The latter is more maintainable.</small>
</blockquote>

To install the default Xerox Phaser 1235 PS printer driver easily you can use the following code from the comand window:

```foxpro
DO WCONNECT
InstallPrinterDriver("Xerox Phaser 1235 PS")
```

**Price:** (about $199 retail)