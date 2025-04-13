PDF generation and display in the browser seem to be a great way to get data displayed on the Web. Unfortunately the browser plug-ins are very flaky with dynamically generated PDF content. 

I can highly recommend the light, weight and free <a href="http://www.foxitsoftware.com/pdf/rd_intro.php" target="top">FoxIT reader</a>. Besides being much smaller than Acrobat in resource usage it also works reliably in the browser with dynamically streamed content. Acrobat 7 also seems to work more reliably, but prior versions had issues displaying dynamic content - especially content that contained POST data or querystring data. With these older readers, dynamic data will in many cases result in blank pages displaying in the browser or the browser forcing the server to fetch the same query result multiple times. 

This is a bug with the browser plug-in model and not specific to Web Connection and there has been no workaround other than **not** directly displaying the result from the server immediately. Instead you can use an intermediate page that points at the PDF file on the server.

```foxpro
lcCompany = UPPER(Request.Form("Company"))

SELECT * ;
   FROM TT_Cust ;
   WHERE UPPER(Company) = lcCompany ;
   ORDER BY Company ;
   INTO CURSOR TQuery
 
oPDF=CREATE("wwDistiller")
lcFile = SYS(2015) + ".pdf"

* ** Print the report to a temporary Web Directory
oPDF.PrintReport("custlist.frx",Server.oConfig.owwDemo.cHTMLPagePath + "/temp/"+lcFile)

* ** This page briefly displays, then redirects to the PDF file
THIS.StandardPage("Report Complete",[<a href="/wconnect/temp/] + lcFile + [">] +;
                  [Click here to view the Customer Report</a>],,;
                  1,"/wconnect/temp/"+lcFile)

* ** Delete files that have 'timed out' (300 secs)
DeleteFiles(Server.oConfig.owwDemo.cHTMLPagePath + "temp/*.pdf",300)
```

This approach uses an intermediary page to hold a link to the generated PDF file which then auto-redirects to the page. This works reliably in all tested situations, but you may have to change the redirect timeout to a slightly longer value than the 1 second specified above in the call to StandardPage. Not re-directing at all and clicking the link works  100% in all browser/plug-in situations.

This approach requires managing file cleanup on the server. Because the PDF page is not served directly, but indirectly referenced you cannot delete it immediately as part of the current request. 

Note the call to DeleteFiles() to clean up the generated PDF files. The PDF file must be generated into a known Web directory which should be a specially set up Web temp directory. Web Connection installs a TEMP directory off the /WCONNECT root by default and I tend to use such a temp directory for any temporary files. DeleteFiles deletes files selectively after a specified period to avoid piling up large number of files. Here 5 minutes is used as the timeout before a file is deleted. Remember users can download the file and save it locally if needed so 5 minutes is plenty.

**Send PDF output to another window**  
Another suggestion for the above example is to send the result to a separate window using a TARGET attribute on your form or HREF link like so:

<pre><a href="GeneratePDF.wwd?date=10-10-2005" **TARGET="PDFView"**>Generate PDF</a>

or:

<form action="GeneratePDF.wwd" **TARGET="PDFView"**>
...
</form>
</pre>

This will send the result from the PDF generation to a new window which is less distracting to the application. This allows the user to close the application window with the PDF inside it rather than using the BACK button which is often unnatural with a PDF.

### Download File rather than Display in Browser
You can also use Response.DownloadFile() to cause the file to be downloaded to the client as a File rather than streamed into the browser for display:

```foxpro
lcCompany = UPPER(Request.Form("Company"))
IF EMPTY(lcCompany)
   lcCompany = UPPER(Request.QueryString("Company"))
ENDIF

SELECT * ;
   FROM TT_Cust ;
   WHERE UPPER(Company) = lcCompany ;
   ORDER BY Company ;
   INTO CURSOR TQuery

* ** WWC_WWPDF is defined in WCONNECT.H
* ** or you can override it here 
oPDF=CREATE([WWC_WWPDF])

* ** For Distiller or GhostScript distiller we need to specify the 
* ** Postscript printer driver here or in a Subclass! Printer must exist.
* oPDF.cPrinterDriver = "Apple Color LW 12/660 PS"

* ** Temporary filename
lcFile = SYS(2015) + ".pdf"

oPDF.PrintReport("custlist.frx",Server.oConfig.owwDemo.cHTMLPagePath + "temp\"+lcFile)

Response.DownloadFile(Server.oConfig.owwDemo.cHTMLPagePath + "temp\"+lcFile,;
                      "application/pdf",;
                      "CustomerReport.pdf")
```