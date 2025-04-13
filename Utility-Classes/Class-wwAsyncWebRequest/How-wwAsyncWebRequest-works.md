Asynchronous events can be used for many operations that take a while to run. The basic idea behind them in a Web application is:

![](IMAGES%5CMISC%5CASYNC.JPG)
<ol>
* Browser submits a request to the server
* Server picks up request and submits it to the event queue
* Server returns a 'I'm working on it' to the browser which is refreshed every x seconds
* External application picks up the event and starts processing it
* Optionally external app updates the status of processing as available
* When done external app updates the event as completed
* Web Server app meanwhile keeps checking for completion 
* When complete the Web Server app picks up the completed result (XML, a data file somewhere, or maybe a PDF or HTML string etc)
* Web Server can optionally perform more processing on the returned data
* Web Server then returns the result to the client browser
</ol>


The following code sequence demonstrates how events
can be set by clients and processed by servers:

**Client initiating the Event:**  
<pre>SET PROCEDURE TO wwAsyncWebRequest ADDITIVE
SET PROCEDURE TO wwXMLState ADDITIVE


* ** Event Initiator code (Web App most likely)
o = CREATEOBJECT("wwAsyncWebRequest")

* ** SubmitEvent saves the request
lcId =  o.SubmitEvent("Test Data","Sumbited at: " + TIME())

* ** If you want to add additional data you can use dynamic properties
o.SetProperty("Name","Rick")
o.SetProperty("Report","custlist")
o.SetProperty("Time",DATETIME())
o.SetProperty("SQL","select * from TT_Cust")

* ** Or your can set properties on the even object directly
o.oEvent.UserId = "rstrahl"
o.oEvent.Title = "Test Event"

* ** If you make manual changes make sure to Save
o.SaveEvent()
</pre>

**Server receiving the event:**  
<pre>* ** Pass in an event id somehow
LPARAMETERS lcID

Application.Visible = .F.

IF EMPTY(lcID)
   RETURN
ENDIF

SET PROCEDURE TO wwUtils ADDITIVE
SET PROCEDURE TO wwXMLState Additive
SET PROCEDURE TO wwAsyncWebRequest Additive
SET CLASSLIB TO wwXML ADDITIVE

loAsync = CREATEOBJECT("wwAsyncWebRequest")

* ** Load the Event
IF !loAsync.LoadEvent(lcID)
   RETURN
ENDIF   

WAIT WINDOW "Simulating long request taking 20 seconds..." TIMEOUT 20

lcSQL = loAsync.GetProperty("SQL")

* ** Run the SQL Statement
&lcSQL INTO Cursor TTCustList

* ** Do something with the output - in this case turn into XML
loXML = CREATEOBJECT("wwXML")
lcXML = loXML.CursorToXML()

* ** Close out the request and pass the return data
* ** into the ResultData property
loAsync.CompleteEvent(lcID,lcXML)

* ** EXIT app
RETURN
</pre>


While waiting for the request to complete on the server the client code polls the server. The polling code can look like this:

<pre>IF loAsync.CheckForCompletion(lcID) = 1
         * ** Display result - XML document return in this case
         Response.ContentTypeHeader("text/xml")

         * ** Retrieve the data from the server and display as XML
         Response.Write(loAsync.oEvent.ReturnData)

	  * ** Alternately you could read any other fields of the oEvent object
         * ** or any property set by the server application with GetProperty()
         RETURN
ENDIF
     
* ** Create the waiting output page
lcBody = "<hr><b>Waiting for report to complete" + ;
         REPLICATE(". ",loAsync.oEvent.ChkCounter + 1) + "</b><hr><p>" + ;
         "This report is now running in another process while leaving the "  +;
         "Web Connection instance running free to process other requests. " + ;
         "It polls back every few seconds to check for completion and when done " +;
         "picks up the result data to be displayed in this case a simulated " + ;
         "long running report..."

* ** Create the 'Waiting...' page. Note the Refresh and Refresh URL parameters
* ** The URL contains a counter so we can abort after x number of tries.
THIS.StandardPage("Running Report",lcBody,,;
                  lnPageRefresh,"AsyncWebRequest.wwd?Action=Check&RequestId=" + lcId)

</pre>