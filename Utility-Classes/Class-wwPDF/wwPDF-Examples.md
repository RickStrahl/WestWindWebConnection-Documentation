wwPDF is very easy to use and requires only a couple of lines of code once the appropriate driver is installed. Note make sure whichever tool you use works interactively as a printer driver before trying any of the classes here.

The following examples use the wwDistiller class, but you can any of the other classes that match your installed PDF generation software.

**Simple Example:**  
This example generates a PDF document on disk and then reviews the file locally.

```foxpro
oPDF=CREATE("wwDistiller")
oPDF.cPrinterDriver = "Apple Color LW 12/660 PS"  && PS driver must be installed for distiller and ghostscript

SELECT * FROM wwDemo\tt_cust ORDER BY company

*** Print the report to a PDF file
oPDF.PrintReport("CustList.Frx","d:\temp\test.pdf","FOR company > 'G'")

*** View the PDF
GoUrl("d:\temp\test.pdf")
```

**Web Example:**  
This example runs a query on the Web Server and returns the result directly back to be viewed inside of the browser.

```foxpro
lcCompany = UPPER(Request.Form("Company"))

SELECT * ;
   FROM TT_Cust ;
   WHERE UPPER(Company) = lcCompany ;
   ORDER BY Company ;
   INTO CURSOR TQuery
 

*** Using Distiller - make sure to install a PostScript printer and use it
*** Slower but more consistent in PDF generation
oPDF=CREATE("wwDistiller")
oPDF.cPrinterDriver = "Apple Color LW 12/660 PS"

lcPDF = oPDF.PrintReportToString("custlist.frx")

loHeader = CREATEOBJECT("wwHTTPHeader")
loHeader.SetProtocol()
loHeader.SetContentType("application/pdf")
loHeader.AddHeader("Content-Length",TRANSFORM(LEN(lcPDF)))
loHeader.AddHeader("Accept-Ranges","bytes")
loHeader.AddHeader("Connection","Close")

Response.ContentTypeHeader(loHeader)
Response.Write(lcPDF)
```

Note that there are some issues in older versions of the Acrobat reader in Internet Explorer displaying dynamically generated content which can result in blank output or multiple requests on the server.

For more information see [PDF Browser display issues](vfps://Topic/_0LC1DIHM7).