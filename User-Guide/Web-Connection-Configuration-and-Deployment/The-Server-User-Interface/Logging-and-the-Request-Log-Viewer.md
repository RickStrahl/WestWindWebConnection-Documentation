Web Connection can log each incoming request at varying levels of log detail and the Server Status Form includes button to start a Request Log Viewer to review.

## Logging Modes
Web Connection logging can capture full request and response data in maximum log mode. There are options for no logging at all to logging the entire request and response. 

Log Modes available are:

* **0 - No Logging**  
No logging.

* **1 - Minimal Request Logging**  
Only the request URL, browser, HTTP Verb, time and duration are logged

* **2 - Full Request Logging**  
The entire request is logged including the request body on POST/PUT operations and full HTTP headers. This can be substantial in size.

* **3 - Request and Response Logging**   
As #2 but also adds the full response and response HTTP headers. 

This value can be set via the `goServer.oConfig.nLogFormat` property at runtime or the `LogFormat=1` setting in the `<yourApp>.ini` configuration file. You can also find the log mode configurable on the Server Status form.

![](IMAGES/ManagementConsole/serverstatus_saverequest.png)

The default is **1 - Minimal Request**, which logs only the most important request data and no post data of the request. For debugging purposes you'll want logging set to the **3 - Full Request and Response Logging** so you can capture the entire request data and response output.

### Recommendations
Your log settings depend on your environment and whether you're running in production or in 

**For production**: It's highly recommended you use **No Logging** or use **Minimal Request Logging** to avoid massive disk usage. Log files get large quickly and can easily overrun FoxPro's 2gb size limit.

**For development**: **Full Request and Response** logging is useful, as it allows you to look at incoming Request and outgoing Response data which is often very useful for debugging. 

> #### @icon-warning Log File Management
> It's important to keep an eye on the Web Connection log file and make sure it doesn't get too big and approaches or exceeds the 2gb limit. Even minimal logging eats up a lot of data quickly. Either pack and reindex your log files regularly from the Web Module Admin page or have a system of backing up log files and then deleting them (Web Connection recreates them when not found).


## The Request Log Viewer
One useful feature is the ability to review the last few requests that were captured, which can be done by enabling logging in the server.

![](IMAGES/ManagementConsole/serverstatus_saverequest.png)


To make it easier to review log data, a separate form can display request information in an organized manner using the Request Log Viewer. 

![](IMAGES/ManagementConsole/RequestLogViewer.png)

The form displays a list of recent requests from the `wwWebRequestLog.dbf` log file and you can click through the requests for more detail.

For each request there are several pages available:

#### Request Info
The default page shows a summary for the request, which shows the URL, HTTP Verb and processing time. This information is displayed for all types of logging.

#### Full Http Response
This page shows the full HTTP Response output from the request.

![](IMAGES/ManagementConsole/RequestLogViewerFullResponse.png)

This is the complete HTTP output - HTML or other data including the HTTP header generated. This is what Web Connection sends to the Web server.

The **Browse** button lets you view the output as HTML. The **Edit** button opens the document in your configured HTML editor.

#### Formatted Request
This tab displays a parsed list of Request properties of all the data the Web Server sent to Web Connection for this request:

![](IMAGES/ManagementConsole/RequestLogViewerFormattedRequest.png)

This can be very useful for debugging to ensure that your application is receiving the data that you think it should be getting.

#### Raw Request
Finally the Raw Request page dispays the raw, unparsed  data that the Web Server sends to Web Connection. This is not super useful, except if you want to capture a raw request to play it back in an API or URL testing tool like [West Wind WebSurge](https://websurge.west-wind.com), [Postman](https://www.postman.com/) or for coded HTTP clients.

![](IMAGES/ManagementConsole/RequestLogViewerRawRequest.png)