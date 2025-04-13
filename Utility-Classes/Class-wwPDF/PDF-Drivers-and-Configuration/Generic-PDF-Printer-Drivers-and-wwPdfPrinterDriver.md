The `wwPdfPrinterDriver` can be used with most PDF printer drivers that are installed into Windows. **Windows 10+** and **Window Server 2016** or later ship with the `Microsoft Print to PDF` driver which now provides a built-in PDF output solution.

This `wwPDF` implementation handles changing the printer, sending a report to the printer and waiting for the completed PDF file which is printed asynchronously to the printer spooler. 

If you're using Windows 10 or Windows Server 2016 or later we recommend you use the `Microsoft Print to PDF driver`. 

We've tested with a few other printer drivers - Nitro, FoxIt - and this driver works with both of them just fine. It should work with **any driver that supports printing by redirecting the print spooler**. One caveat is that the driver has to be able to run without UI prompts. Some older drivers cannot bypass the file selection dialogs and those will not work. However most newer drivers properly support passthrough file names that are sent as part of the print job.

### Example
The following is an example that uses the Microsoft Print to PDF driver.

```foxpro
oPDF=CREATEOBJECT("wwPdfPrinterDriver")     

*** Specify the printer **name** from your Printer List
oPdf.cPrinterDriver = "Microsoft Print to PDF"

*** Optional - wait for doc to finish in milliseconds
oPdf.nMaxWaitTime = 10000

lcFile = SYS(2023) + "test.pdf"

IF !oPDF.PrintReport("custlist.frx",lcFile)
   ? oPDF.cErrorMsg
ENDIF

*** Desktop: Open the file and display it
* GoUrl(lcFile)

*** Web Connection Response Output:
*** Download the file from disk to Web clients
Response.DownloadFile(lcFile,;
                         "application/pdf",;
                         "CustomerReport.pdf")  
```

## Installation Checklist for Microsoft Print to PDF

* You run only on Windows 10 or Windows 2016+ 
* Make sure you install `Microsoft Print To PDF` Printer
* Make sure the **Printer Spooler Service** is running/automatic
* Make sure printer is accessible for other users
*(Printer Properties->Security then add users)*
* Make sure you write PDF files into a folder where you have permissions to write


Note 3rd party PDF printer drivers typically work on older versions. The specs above are for the Microsoft PDF driver.

### Installing a specific printer Driver
To install the default `Microsoft Print To PDF` driver easily you can use the following code from the command window:

```foxpro
DO WCONNECT
InstallPrinterDriver("Microsoft Print To PDF")
```

Or you can use the Console from the Windows command line:

```
Console.exe INSTALLPRINTER "Microsoft Print To PDF"
```

## Other PDF Printers known to work

* [PDF Architect](https://www.pdfforge.org/)   (free version available)
* [Nitro Pro PDF Editor](https://www.gonitro.com/)  (great PDF Editor includes driver)