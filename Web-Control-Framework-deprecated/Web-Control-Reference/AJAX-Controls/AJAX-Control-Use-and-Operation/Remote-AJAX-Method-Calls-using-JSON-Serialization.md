Using the wwAjaxMethodCallback control allows making client side calls to server side methods in your Web Connection application using powerful Remote Procedure class that return data using the JSON (Java Script Object Notation) protocol. JSON provides data in a JavaScript compatible text format which is more efficient than XML and lets client side JavaScript code execute the JSON string directly.

This powerful mechanism allows your applications to pass data into client side pages to provide inline updates to data for common operations like updating page content, providing live validation against data from the server (for example validate a valid range value or checking for duplicates on new entries etc.) and even for handling typical data update tasks in a more client server centric way than is typically performed in Web applications.

You can use one control to handle multiple callbacks. If you have simultaneous callbacks it's recommended you use separate controls for each simultaneous callback.

Here are the steps to call a remote method:
<ul>
* Add a wwAjaxMethodCallback control to the page
* Create a method in your server page class that is to be called from the client
* Optionally add each method you want to allow in AllowPageMethods property
* Hook up the Callback.callMethod() call to initiate the callback
* Implement a Callback and Error handlers that receives the result or error
</ul>

Start by adding a wwAjaxMethodCallback control to the page from the ToolBox or using code like this:

```html
<ww:wwAjaxMethodCallback ID="Proxy" runat="server" 
                        PostBackMode="PostMethodParametersOnly"
                        PostBackFormName="form1"
                        AllowedPageMethods="Helloworld,AddNumbers,GetDeveloperList,GetDeveloper,SaveDeveloper">
</ww:wwAjaxMethodCallback>
```

If you want to post back form data from the page you can set the PostBackMode to "Post" or "PostNoViewState" and provide the name of the HTML form that is to be posted back. ScriptLocation points at the wwScriptLibrary.js - WebResource is the default and means that the library is retrieved from the Web Connection server which serves it in GZipped format.

You're now ready to start calling remote methods. So lets set up the server side method. What else than a Helloworld method? <g>

```foxpro
FUNCTION Helloworld(lcName)
RETURN "Hello " + lcName + ". Time is: " + TimeToC(DATETIME())
* could also use: this.txtSayHello.Text
ENDFUNC

FUNCTION AddNumbers(lcNumber1,lcNumber2)

lnNumber1 = VAL(lcNumber1)
lnNumber2 = VAL(lcNumber2)

IF lnNumber1 = 0 AND lcNumber1 != "0" OR ;
	lnNumber2 = 0 AND lcNumber2 != "0" 
	* ** Throw an error which is captured
	ERROR "Invalid numeric input values: " + lcNumber1 + " + " + lcNumber2
ENDIF

RETURN lnNumber1 + lnNumber2
ENDFUNC
```

Very simple. You just create your method and have it return normal parameter types. Note that values sent from the client are passed up as strings only. Values returned from the server are typed values via JSON serialization.

On the client you now need to hook up the call to some sort of client side action. Let's use a textbox and button for our Helloworld sample:

```html
<strong></strong>Enter your name:
<ww:wwWebTextBox ID="txtSayHello" runat="server" Width="178px">John Doe</ww:wwWebTextBox>
<input id="btnSubmit" type="button" value="Say Hello" 
         onclick="HelloWorld();" />
<br />    
<br />
<div id="lblHelloWorldResult" class="errordisplay" style="padding:10px;width:500px;display:none;"></div> 
<br />
```

The script code to handle the callback then looks like this:

```cs
<script language="JavaScript">
function HelloWorld()
{
	Proxy.callMethod('HelloWorld',[$('#txtSayHello').val()],Helloworld_Callback,OnError);
}
function Helloworld_Callback(Result)
{
	alert(Result);
}
function OnError(Exception)
{
	 alert(Exception.message);
}
</script>
```

The key above is the Proxy.callMethod call which initiates the callback to the server. The Proxy object is an automatically created instance that is available and configured ready to make a callback. 

callMethod takes these parameters:
<ul>
* The server method to call on the object specified
* An array of parameters  (or [])
* A Callback function that is called on return 
* A Error function that is called on errors
</ul>

The Callback handler should point at the Javascript function that receives the result from the server side function call. This function receives a single parameter that is the properly typed result value. So if your server side function returns a dateTime value it will be returned as a Date. If you returned an object you will receive an object on the client. If you returned a cursor a cursor object (an array of objects) will be returned etc.

Note that  the server call asynchronous. The call is started and control returns immediately to the page. Only when the call is completed on the server is a callback made to your callback handler.

**Errors**  
If an error occurs the error handler function is called. The error handler is optional - if you don't provide it errors are 'eaten' - nothing happens but the request just never returns. It's recommended that you hook up an error handler so that you at least can know that something went wrong even if you choose to ignore the errors. Note that you can have all of your callbacks point at the same error handler if you choose. So you'll typically just have one OnError handler. 

The Error handler receives and error object parameter which contains a .message and .isCallbackError property. The message property contains an error message depending on where the error occurred. If the error occurred on the client as part of the callback - XmlHttp or trying to parse the response data etc. - an error is generated on the client. If an error occurs on the server during the execution of the remote method call the Exception is captured and passed back to the client. This is equivalent of the FoxPro MESSAGE() function retrieved off an Exception object.

### Returning a FoxPro Cursor
As mentioned the control manages to serialize result values into proper types that are accessible on the client. You can also return Cursors by using a specialized syntax. To return a cursor from the server set up a function like this:

```foxpro
FUNCTION GetDeveloperList()

SELECT pk,Company,Name FROM wwDevRegistry ORDER BY Company INTO CURSOR TQuery

* ** Return the cursor as an object
RETURN "cursor:TQuery"
ENDFUNC
```

Notice the "cursor:CursorName" syntax. The callback manager picks up this result value and parses out the cursor name and then constructs an object out of it. The structure of the result object is:

Table.Rows[1].fieldname

An array of rows is created with objects for each of the rows in the table. Each field of the row is mapped to a value of the proper type. So you can do:

```cs
Tables.Rows[1].entered.toLocaleDateString();
```

Note that all field names are returned to the client in lower case. The Rows property is an exception due to a compatibility issue between the Fox and .NET versions of the library.

Here's an example of what the client code populating a listbox from a cursor result looks like using the listSetData jQuery plugin in the client library:

```cs
function GetDeveloperList_Callback(Result)
{
    // * ** Result is a FoxPro Table represented as an object array

    // * ** Use a wwList and DataBind directly to the table returned
    $('#lstCustomerList').listSetData( Result, { dataTextField: "company",dataValueField: "pk"} );
}
```

This code uses the setListData plug-in  which allows databinding on the client side to a cursor object or array elements and is a lot easier than manually assigning values to the list with JavaScript code. But realize that of course this is optional - if you want to iterate over the list and assign list values manually you can do that as well. You can look at Rows[x] to get each row's item as an object and assign the values manually to whatever you choose.

### Returning Objects
You can also return objects from the server. The wwAjaxMethodCallback  control tries to parse the object into a JSON string. Currently support is limited to simple objects and simple types including support for hierarchical simple objects.

Objects from the FoxPro server are persisted to the client with the same structure as the server, but all property names become lower case due to the limitations in Visual FoxPros object parsing routines. Receiving an object result on the client might look like this:

```cs
function GetDeveloper_Callback(Result)
{
        // * ** Result is an Object        
        $("#txtCompany").val(Result.company);
        $("#txtName").val(Result.name);
        $("#txtAddress").val(Result.address);
}
```

### Passing Data to the Server
You have two ways to pass data to the server:
<ul>
* POST Form Variables on the page
* Parameters in the array of parameters
</ul>
The wwAjaxMethodCallback control has several options on how data is sent from the client to the server. The default for method callbacks is POST which posts all the data that is on the current form. Other options including sending only method parameters which are encoded into JSON. Using POST is useful if you have a bunch of information on the form that is already filled. JSON object parameters are useful if you only have a few values or if you have objects that have been modified and need to be updated back to the server.

**Passing JSON values Limitations**  
Please also note that the parameters supported for calling the server in the array passed are somewhat limited. All simple parameters (string, numbers, dates, bools etc.) work without any problems. Simple objects and any objects using only simple types also should work including hierarchical objects. However, arrays and collections may or may not work. Simple single level object arrays are supported. Ragged arrays or arrays that contain sub objects with further arrays are not.

The server side calls on the wwJSONSerializer class to perform the deserialization of data from the client. The wwJsonSerializer code on the server attempts to create an object from the data passed by creating an object of type BLANK and adding properties as the data is parsed.

Note that you can also pass 'parameters' in an indirect way via POST variables. In many situations this is actually easier than parsing the data into parameter or objects and then passing these up to the server. Instead you can simply POST form vars directly to the server and let the server side method pick up the values out of the POST buffer. If the values are contained in server side controls like wwWebTextBox for example, the server code can even use the control names directly:

```javascript
function SaveCustomer()
{
   // * ** Must pass the CustId for reference
   Proxy.callMethod("SaveDeveloper",[CustId],SaveDeveloper_Callback,OnError);
}
```

As you can see this code doesn't pass the customer data to the server at all, but the data from the TextBoxes is in the POST buffer. The server can pick up the values like this:

```foxpro
FUNCTION SaveDeveloper(lcID)

IF EMPTY(lcID)
	ERROR "No Developer Id passed."
ENDIF	

loDeveloper = CREATEOBJECT("wwDevRegistry")
IF !loDeveloper.Load( VAL(lcID) )
	ERROR "Invalid Developer ID:  " + lcID
ENDIF

* ** Note: We're using the Controls in the AJAX callback
* **          Page callbacks go through the Page Pipeline until OnLoad() in the 
loDeveloper.oData.Company = this.txtCompany.Text
loDeveloper.oData.Name = this.txtName.Text
loDeveloper.oData.Address = this.txtAddress.Text

IF !loDeveloper.Validate()
	ERROR loDeveloper.cErrorMsg
	RETURN .F.
ENDIF

loDeveloper.Save()

RETURN "Developer has been saved: " + loDeveloper.oData.Name
```

Note that the values are automatically accessible in the appropriate controls like this.txtCompany.Text because wwAjaxMethodCallback processes the remote method calls in OnLoad() after view state and POST data has been applied to controls.

But be aware that with some controls like lists the value may not be available as the list data may not be assigned in a callback. If your callback code by passes databinding (as it should usually), a list won't contain any data and hence no selected value either. In those cases you can always use explicit retrieval of the value from the POST buffer:

```foxpro
lcCustomerId = Request.Form(this.lstCustomerList.UniqueId)
```

As you can see there's a fair amount of flexibility to return results to the client. Returning these typed values make it much easier to deal with client side processing of the data.

### Multiple simultaneous Method Calls
If you plan on making multiple simultaneous calls it's recommended that you create a new instance instead of using the existing fixed instance. For example:

```javascript
function HelloWorld()
{
     // create a new instance instead of using the predefined Proxy instance
     var MyInstance = Proxy_GetCallback()
     MyInstance.callMethod('HelloWorld',[$('#txtSayHello').val()],Helloworld_Callback,OnError);
}
```

The name of the factory function is *NameOfControl*_GetCallback(). This function is internally called to create the default proxy and you can call it yourself at any time to retrieve a new instance. By calling the factory instead of reusing the global reference you are creating a new instance that has its own http context and async state. For most operations this is not necessary, but if you have many simultaneous requests going it's better to explicitly create new instances to ensure there's no possibility of instance cross talk.