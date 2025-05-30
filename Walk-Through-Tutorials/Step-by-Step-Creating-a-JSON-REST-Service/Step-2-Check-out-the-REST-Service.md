At this point you have a new REST service. On the surface things look very similar to a regular project. You get a new Process class and a simple sample HTML page.

Go to the local project page now and open:

<a href="http://localhost/customerdemo/default.htm" target="top">http://localhost/customerdemo/default.htm</a>

And you're greeted by a familiar sample page:

![](/images/stepbystep/CustomerDemo_HomePage_REST.png)

However, now that you've created a REST service when you click the *Hello World Test Page* link, you won't get the familiar HTML page, but instead see a JSON result:

![](/images/stepbystep/RestServiceHelloWorldJson.png)

> ### @icon-info-circle Viewing JSON in your Browser
> Most browsers don't preview JSON natively and you need to add a plug-in or make some setting changes. For Chrome and Firefox consider adding the <a href="https://chrome.google.com/webstore/detail/json-viewer/gbmdgpbipfallnflgajpaliibnhdgobh?hl=en-US" target="top">JsonViewer plugin for Chrome</a> (shown in the screen shot) or <a href="https://addons.mozilla.org/en-us/firefox/addon/jsonview/" target="top">JsonView plugin for FireFox</a>. Internet Explorer requires a <a href="http://weblog.west-wind.com/posts/2011/apr/01/displaying-json-in-your-browser" target="top">registry tweak</a>. Microsoft Edge natively displays JSON as raw text.

The result is a JSON object which is generated by the REST process and the TestPage method in the CustomerRestService Process class we generated with the Wizard:

```foxpro
*********************************************************************
FUNCTION TestPage
***********************
LPARAMETERS lvParm
*** Any posted JSON is automatically deserialized
*** into a FoxPro object or value


*** Simply create objects, collections, values and return them
*** they are automatically serialized to JSON
loObject = CREATEOBJECT("EMPTY")
ADDPROPERTY(loObject,"name","TestPage")
ADDPROPERTY(loObject,"description",;
            "This is a JSON API method that returns an object.")
ADDPROPERTY(loObject,"entered",DATETIME())

*** To get proper case you have to override property names
*** otherwise all properties are serialized as lower case in JSON
Serializer.PropertyNameOverrides = "Name,Description,Entered"


RETURN loObject
```

### What's Happening
This is a typical REST service endpoint method. The idea behind the Web Connection REST service is that you create a method that takes an optional **single input parameter** that can be a simple value or complex object or collection, do some processing and then returns back a single value as a result. It's that simple.

The Web Connection REST service manages all the deserialization of JSON into the passed parameter and converting your result value into JSON. So if the client sends a JSON object, your FoxPro method receives a FoxPro object that maps that JSON object. Your FoxPro code returns a FoxPro value, object or cursor and the JavaScript code receives a JSON object it can then use. We'll look at the JavaScript code to do this later in this tutorial.

As with HTML based Web Connection Process methods the name of the method maps to the script name that acts as the HTTP endpoint. So `TestPage.csvc` maps to the `TestPage` method in the CustomerDemoService class which is mapped to the `.csvc` extension/script map. So any method that ends in `.csvc` maps to a method in this class.


> ### @icon-info-circle Testing JSON APIs with <a href="http://websurge.west-wind.com/" target="top">West Wind Websurge</a>
> Once you start creating a JSON API one thing you likely want to test the API endpoints. There are a number of ways you can do this, but if you want a nice GUI to let you test and store your API requests check out **West Wind Websurge**. Websurge is a load and API testing tool and it makes it very easy to create or capture API requests and then play them back on demand either individually, in batch or even under heavy load.
> ![](/images/stepbystep/WebSurgeRESTTesting.png)