The progress bar control is a server control that also includes client side functionality. The control can be set through server properties or by calling client side functions to update the control. The control also loads an internal wwWebAjax control so it can communicate with the server for retrieving progress information by [using Remote Page Methods](vfps://Topic/_1Q1040S1T) that call methods on the server page and return the results back to the client.

### Server Control Behavior
The control works both as a client and server control. You can use the control as a server only control simply by setting its properties. You can set the percentage and message and when the page is rendered the control will properly render the settings of the control. The server settings are always used for first time rendering so if you set settings in the designer these settings affect the initial view.

As a server control the control can be useful in scenarios when you have a long, multi-step process where you want to show users your progress of the whole. You can use the control to display the current status as you go along. Note for server only behavior a Postback is required to update the control values.

**Client Side Behavior**  
In addition to the server side functionality the control also provides an UpdateProgressBar() function on the client side that can be used to update the control through client script. You basically pass in the name of your control as a string, a percentage and text and the control will be updated on the client via script code:

```cs
UpdateProgressBar("Progress",50,"Halfway done!");
```

In addition the control also loads a wwWebAjax control in Page Callback mode so you can make AJAX callbacks to the server. 

To demonstrate the functionality of this control I'll use the ProgressBar.wcsx sample from the Web Connection demo. You can access it with:

<a href="http://localhost/wconnect/webcontrols/progressbar.wcsx" target="top">http://localhost/wconnect/webcontrols/progressbar.wcsx</a>

### Interactive Messaging
The above functionality is implemented with a combination of client side and server side functionality through essentially three basic steps that are implemented both on the client and server:
<ul>
* StartProcessing
* UpdateProgress
* ProcessComplete
</ul>
All 3 of these are implemented both with client and server functionality that is specific to the operation at hand.

Let's assume there's a wwWebProgress control on the form named Progress and it will update every half a second (500 milliseconds). It'll go out and ping the server for completion. On completion a separate request is going to be run to retrieve a result from the server, in this case in the form of a DataGrid with data to be displayed in the page.

The process starts with StartProcessing() which starts processing of messages which is inititated on the client. To do this set up a client side function block like this:

```cs
<script type="text/javascript">
var Cancelled = false;
var TimerInterval = 500;

function StartProcessing()
{
    // * ** Progress_Ajax is the name of the Progress Control _Ajax for the wwWebAjax instance
    CallMethod("Progress_Ajax","StartProcessing",StartProcessingCallback);
}
function StartProcessingCallback(Result)
{    
    if (Result == null)
    {
        alert('Invalid Result');
        return;
    }
    if (Result.iscallbackerror)
    {
        alert(Result.message);
        return;
    }
    
    // * ** Chain events
    window.setTimeout( GetProgress,TimerInterval);           
}       
...
</script>
```
The StartProcessing function is the starting point that starts the process. You can hook the call to the function to a button's onclick event:

```html
<input id="btnStartProcessing" type="button" value="Start Processing" onclick="StartProcessing()"  />
```

The function uses the wwWebAjax control to make a callback to the server. The code above says: Use the Progress controls' Ajax control, to call the StartProcessing method on the server's Page object. In this case there are no parameters, so the only final parameter is the name of the Callback function pointer that is called when the call returns. The call is asynchronous, hence the callback pointer - the StartProcessCallback function is called when the server side code returns.

The server side StartCallback function is just FoxPro code that is used to set up the initial processing. In this sample the processing consists of merely increasing a counter value so the code is super simple:

```foxpro
FUNCTION StartProcessing()

* ** Create a counter we can increment and store in Session
Session.SetSessionVar("Simulate_Counter","0")

* ** The ID is not used here, but usually you would 
* ** generate an ID to pass back to the client so
* ** the client and server have context
lcId = SYS(2015)
Session.SetSessionVar("progress_Id",lcId)

* ** Store start seconds so we can estimate time to completion
Session.SetSessionVar("progress_StartTime",TRANSFORM(SECONDS()))

RETURN lcId
ENDFUNC
```

Really the only value that's important in this sample is the counter. The ID isn't used, but I show it here, because in most scenarios you'll want to pass a 'request id' of some sort to the client so the client and server know which request they are dealing with. This isn't necessary here, but it's common in messaging scenarios.

The client calls this method with the CallMethod() call from the client. Web Connection processes this method and returns the result back to the client. If you look at the StartCallbackCallback method, you'll see a Result parameter which contains the result value - in this case the returned ID - or null or an exception object. The StartCallback code checks for errors before accepting the request. If the call is good, the request is then queued up for progress checking. 

The call to window.SetTimeout() delays operation of the call to GetProcess, which is used to initiate a request to the server to ping for progress information. Note that requests are chained, so when one request completes the next is 'queued' with window.setTimeout which delays the operation by the timeout period.

So in 500 milliseconds then GetProcess fires on the client:

```cs
function GetProgress()
{
  CallMethod("Progress_Ajax","UpdateProgress",Cancelled, GetProgressCallback);
}
function GetProgressCallback(Result)
{
    if (Result == null)
    {
        alert('Invalid Result');
        return;
    }
    if (Result.iscallbackerror)
    {
        alert(Result.message);
        return;
    }
    
    UpdateProgressBar("Progress",Result.percent,Result.message);
    
    if (Result.cancelled)
    {
        alert('Operation cancelled on the server.');
        $('btnStartProcessing').disabled = false;
        $('btnStopProcessing').disabled = true;
    }
    else if (Result.completed || Result.percent >= 100)
       ProcessComplete();
    else
       window.setTimeout( GetProgress, TimerInterval );
}
```
 
The idea here is the same as in the first call. CallMethod is used to call a method of the Page on the server in this case UpdateProgress. Here a single parameter Cancelled is passed to the server with the idea being that the client can cancel the request. The server can check the cancelled flag and act accordingly.

The server code looks like this:

```foxpro
FUNCTION UpdateProgress(lcCancel)

lnCounter = VAL( Session.GetSessionVar("simulate_Counter") )

LOCAL loArgs as ProgressEventArgs
loArgs = CREATEOBJECT("ProgressEventArgs")

* ** Parameters come back as literal strings only 
* ** so we have to parse 'true'
IF lcCancel == "true"
	loArgs.Cancelled = .t.
	loArgs.Percent = lnCounter
	loArgs.Message = "Operations has been cancelled at " + TIME()
	RETURN loArgs
ENDIF

* ** Completed
IF lnCounter >= 100 
	loArgs.Completed = .T.  
	loArgs.Message = "Completed"
	loArgs.Percent = 100
	RETURN loArgs
ENDIF

* ** HERE YOU WOULD NORMALLY DO YOUR CHECKS FOR ASYNCHRONOUS
* ** OPERATIONS. FOR EXAMPLE, YOU COULD USE wwWebAsyncRequest
* ** TO OFFLOAD PROCESSING TO ANOTHER PROCESS AND CHECK FOR
* ** COMPLETION ON THE EXTERNAL PROCESS

* ** In this sample - we just increment a counter
lnCounter= lnCounter + 3
loArgs.Percent = lnCounter 

lnSeconds = VAL( Session.GetSessionVar("progress_StartTime") )
lnSeconds = INT( SECONDS() - lnSeconds )

* ** Try to calculate approximate time left on current percentage done and time
lnLeft = INT( (lnSeconds * (100/lnCounter) ) - lnSeconds  + 1 )

* ** Just send a progress message
loArgs.MESSAGE = "Working... <small>(" +  TRANSFORM(lnLeft) + " seconds remaining)</small>" 
Session.SetSessionVar("simulate_Counter",TRANSFORM(lnCounter))

RETURN loArgs
```

Note that the Cancelled parameter is passed to the server as a string so it has to be parsed as "true" rather than .t. The key to this method is the ProgressEventArgs object which is used to communicate results back to the client code. The object contains properties for Percent, Message, Cancelled etc. that allow the server to tell the client what's going on on the server.

The server then checks for various status situations. Has the request been cancelled? If so stop counting and return a cancel acknowledgement back. Are we done? Set completion settings on the object. Just processing? Calculate the percentage, and create a message for the client. The percentage and message can then be used by the client to update the Progress display.

The client code (GetProgressCallback()) then receives the ProgressEventArgs object as a parameter (Result). Again first check for errors. Next the Progress bar is updated with the data returned in the Result structure. Note that on the client the object can just be accessed using object.property syntax with all property names being lower case (a VFP limitation). Next specific checks are made to check if the request has been cancelled or completed. If cancelled we're done. If completed we chain to the ProcessComplete() function which completes the request by calling the server to get the final result.

The most common path through this routine will be to simply ping the server again. So the code simply queues up a call to itself at the next timeout interval:

```cs
window.setTimeout( GetProgress, TimerInterval );
```

And so the progress bar gets updated. Every 500 milliseconds (or whatever you set) the client calls the server and the server provides new information to display for the client.

So when the request is complete - 100% is reached - the final thing to do in this case is to retrieve a result. Note this may not be necessary if the operation was truly one way asynchronous. In this example the server returns a datagrid of data. The client script looks like this:

```cs
function ProcessComplete()
{
	CallMethod("Progress_Ajax","ProcessComplete",ProcessCompleteCallback);
}
function ProcessCompleteCallback(Result)
{
	$("lblResult").innerHTML = "<center>" + Result + "</center>";
	UpdateProgressBar('Progress',100,"Customer List Download complete");
}
```

Nothing fancy here. The callback is made to the server ProcessComplete method which looks like this:

```foxpro
FUNCTION ProcessComplete()

* ** Just run a query and render into grid and return HTML 
SELECT Company,Careof,Phone FROM TT_CUST INTO CURSOR TQuery

LOCAL loDg as wwWebDataGrid
loDg = CREATEOBJECT("wwWebDataGrid")
loDg.AUTOGENERATECOLUMNS = .t.
loDg.CssClass = "blackborder"
loDg.DataSource = "TQuery"
loDg.DataBind()

* ** Render the DataGrid into a string
lcOutput = loDg.Render()

* ** And return back as string result from this client callback
RETURN lcOutput
ENDFUNC
```

The method simply runs a dummy query to generate some data, and the creates a wwWebDataGrid and renders it into HTML, which is returned as the result of the method call - an HTML string. 

The ProcessCompleteCallback() function on the client picks up this HTML and simply assigns it to an empty <div> control that is placed on the page.

```html
<div id="lblResult"></div>
```

and is assigned like this:

$("lblResult").innerHTML = "<center>" + Result + "</center>";

Note that $() is simply a shortcut for the HTML DOM document.getElementById() function. All this code does is take the result and assign it to the <div> tag plain and simple.

And that's all it takes. The process seems a little daunting but if you actually look at the code involved you'll realize that there's actuall very little code for this behavior and the process is fairly straightforward to implement.