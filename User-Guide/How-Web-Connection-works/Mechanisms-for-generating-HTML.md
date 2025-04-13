Web Connection includes a number of very different ways of generating HTML/HTTP output from raw output of low level strings to very high level mechanisms that can render entire forms as DHTML. Keep in mind you don't need to use all of these mechanisms - choose whatever will work best for you application.

Here are the choices available:

* **Raw HTML output using the [Response.Write()](vfps://Topic/wwResponse%3A%3AWrite) method**  
This method basically lets you create string output and send it directly into the HTTP output stream. While this can be tedious to code in many cases, this typically yields the best performance and also allows you to use standard string creation techniques to build your output. At minimum your request would need one Response.Write() call. This mechanism works best for pure 'data driven' applications and distributed applications that rely on fast performance more than anything else. Every request must return some data. The [wwResponse](vfps://Topic/Class%20wwResponse) class includes many high level methods that generate a lot of HTML code with a single method call. ShowCursor() and HTMLHeader() are some examples of this. 

* **MVC: HTML Script and Template pages**  
Web Connection supports MVC (Model View Controller) style programming via the Scripts and Templates that act as a view engine. Using `Response.ExpandScript()` and `Response.ExpandTemplate()` you can hand off HTML rendering to HTML code templates that mix HTML and FoxPro code. MVC uses **controllers** which are simple Web Connection Process class methods to coordinate **model data** from your database or business objects, and the **view rendering** using the above Script and Template methods. MVC is a very popular pattern and templates support a host of features like **partial pages**, **layout/master pages**, and **sections**.

* **Web Control Framework**  
The most sophisticated processing engine built ontop of the Web Connection Framework is the [Web Control Framework](vfps://Topic/_1LN1DG5DR). It provides a rich programming model for form's based HTML generation and event management. The framework is similiar in behavior to ASP.NET and provides many features that make Web Pages appear more like Desktop Forms applications rather than Web application. In this framework you use visual editors in Visual Studio or Visual Web Developers to design pages in a rich environment using controls and property sheets to set property values in a WYSIWYG environment and controls include sophisticated databinding support. You can also hook up events on controls like a button click or drop down list on change event for example and hook it to server code. Web Connection generates classes from the visual representation in the designer and manages many aspects of page state for you and firing events on your server FoxPro code. 



* **Full Page generation Methods**  
The wwProcess class also supports several full page generation methods that create complete HTTP document with a single method call. We already saw the StandardPage() and ErrorMsg() methods that create fully self contained pages. Response.ExpandTemplate() and Response.ExpandScript() also fall into this category of creating fully self contained pages.

* **Adobe Acrobat/PDF Generation**  
In addition to HTML generation you can also use the wwPDF40 class to generate PDF documents (Adobe Acrobat Portable Document Format) from Visual FoxPro reports. These reports are generated on the fly using the PDFWriter printer driver to output a report into a PDF document. This option works great for quickly moving reports to the Web, but requires that clients have the PDF viewer (free, but 4megs) and the PDFWriter software which is part of the full Adobe Acrobat package (about $200 street). In addition, report generation is generally slow and resource intensive since the VFP report writer needs to fire up and run the actual reports. Still, for some reports - especially those that require exact positioning and full text formatting PDF output is one of the few available options.

With the flexibility that these options provide come choices that you have to make. 

**Best Performance:**  
Raw HTML output

**Maximum Flexibility:**  
Web Control Framework
Fox Class code plus Templates or Scripts
Raw HTML output
both in combination (like Guest application and wwThreads)

**Easiest migration from Desktop:**  
Distributed XML Application (Fat Client -> Web Server)
DHTML form rendering
PDF Report Generation
Web Control Framework (conceptual similiarity to desktop)

**Frequent changes by non-programmers:**  
Web Control Framework
Scripting and templates

**Restrictive Server environment where compilation is a problem**  
Scripting and templates

**Distributed Applications**  
wwXML generation
wwIPStuff DBF encoding
Raw HTTP output

<H3>Summary - My choices</H3>
Starting with Web Connection 5.0 our recommendation is to use the Web Control Framework wherever possible as it provides maximum flexibility and quick development plus a rich design environment. However, the framework is a little more complex to get started with and to really take advantage of it requires Visual Studio 2005 or Visual Web Developer. The Web Control Framework is likely to be the main focus of future extensions to Web Connection as well.

The Web Control Framework has a fair amount of processing overhead compared to raw HTML output or using an ExpandTemplate() type approach. Keep in mind though that you can mix and match - it's quite possible to have some requests fire raw HTML processing with others going against the Web Control Framework.

If complex reports or exact printing is required for the application PDF rendering is a huge timesaver and I'll take advantage of the ability to quickly get output generated in a rich format. PDF is the only reliable choice if precise client printing for forms is required.

In distributed applications speed tends to be the most important factor as well as flexibility to convert data into formats like XML or encoded DBF files so that they can travel over the wire. Here tools like wwXML and wwIPStuff handle the data conversion mixed with Raw HTML generated typically with the Response.Write() method to write out the raw data into the HTTP output stream.

If you're not sure which approach to use stop by the <a href="whttp://www.west-wind.com/" target="top">message board</a> and solicit some input specific to your scenario. Lots of folks visit there with experience and insight that can provide valuable feedback.