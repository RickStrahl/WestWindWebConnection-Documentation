This class makes it easy to call JSON REST services with Visual FoxPro. The class manages JSON serialization and HTTP calls in a simple single method interface. 

You can use the class directly using `CallMethod()` method to call a service directly, or - better - create a subclass and build a dedicated service class for multiple calls.

For a more detailed look please check out the following blog post:

* [Calling JSON REST Services with FoxPro and wwJsonServiceClient](http://west-wind.com/wconnect/weblog/ShowEntry.blog?id=920)

You can use this class to:
 
* Directly call JSON REST services
* Subclass it and implement service methods that map a service

#### Directly call a JSON REST Service
The direct approach just uses the `CallService()` method directly.

A simple HTTP GET data retrieval could look like this:

```foxPro
*** Load library and dependencies
DO wwJsonServiceClient

loProxy = CREATEOBJECT("wwJsonServiceClient")

lcUrl = "https://albumviewer.west-wind.com/api/artists"
loArtists = loProxy.CallService(lcUrl)

IF (loProxy.lError OR ISNULL(loArtists))
   ? loProxy.cErrorMsg
   RETURN
ENDIF   

*** Service returns an array which is turned into a FoxPro collection
? loArtists.Count  && Collection 
FOREACH(loArtist in loArtists)
   ? loArtist.ArtistName
   ? loArtist.Description
   ? loArtist.ImageUrl
   ? "---"
ENDFOR
```

#### Sending Data to the Server
You can also pass data to the service. JSON REST endpoints that expect data typically expect a single JSON parameter - which can be an object with many sub properties - to represent input values. To call the service with an object parameter for example, simply provide a FoxPro object that maps the structure of the JSON expected by the service. `wwJsonServiceClient` automatically serializes the FoxPro object/value into JSON for you.

To send data to the server, simply pass the `lvData` parameter in `CallService()` with a FoxPro value or object and specify the HTTP verb to be used (typically `POST` or `PUT`):   

```foxpro
loProxy = CREATEOBJECT("wwJsonServiceClient")

lcUrl = "http://albumviewer.west-wind.com/api/album"
lvData = loAlbum && FoxPro object with Album Data
lcVerb = "PUT"   && HTTP Verb

*** Update the Album with the loAlbum data we're sending
loAlbum = loProxy.CallService(lcUrl,lvData,lcVerb)

IF (loProxy.lError)
   ? loProxy.cErrorMsg
   RETURN
ENDIF   

*** Use the updated result album
? loAlbum.Title
? loAlbum.Description
```

#### Customizing the HTTP Request
The Service client class has an `.oHttp` property that handles the HTTP request and is loaded on initialization. You can access this existing instance, or create a new one (using `.CreatewwHttp(loNewHttp)`) to customize the behavior of the HTTP request:

```foxpro
loProxy = CREATEOBJECT("wwJsonServiceClient")
loProxy.oHttp.AddHeader("x-custom","Custom Value")
loProxy.oHttp.nTimeout = 10
```

You can also override the `CreatewwHttp()` method in a subclass to provide re-usable common behavior for the `oHttp` instance.

#### Customizing the JSON Serializer
Likewise you can also use the `.oSerializer` property to customize serialization behavior.

```foxpro
loProxy = CREATEOBJECT("wwJsonServiceClient")
loProxy.oSerializer.PropertyNameOverrides = "lastName,firstName,lastAccess"
```

You can override the `CreateSerializer()` method in a subclass to provide re-usable common behavior for the `oSerializer` property.
#### Creating a Service Proxy Class
If you are calling a service that has many methods it's recommended that you create a separate class for all the service calls, similar to a service *'business object'* that isolates all logic related to the service in a class. This does several things:

* Single logical location for all Service related code
* Consistent interface for calling service methods
* Easy reuse for service calls throughout application

To use, subclass `wwJsonServiceClient` and implement your service methods that then using `CallService()` to call JSON service endpoints. The idea is that each service method you implement completely abstracts the service logic so the calling code simply calls a method with a parameter and a result value - all the service logic is handled internally to the service wrapper class.

Here's what a class looks like calling a couple of service methods:

```foxpro
DEFINE CLASS CustomerServiceClient as wwJsonServiceClient

cServiceBaseUrl = "http://localhost:23332/localizationService.ashx?method="

FUNCTION GetResourceSets()
    LOCAL loResult
    loResult = this.CallService(this.cServiceBaseUrl + "GetResourceSets")

    IF (this.lError)
       RETURN .NULL.
    ENDIF
    
    RETURN loResult
ENDFUNC

FUNCTION UpdateResource(loResource)
    LOCAL loResult
    
    *** Service returns update resource
    loResult = this.CallService(this.cServiceBaseUrl + "UpdateResource",loResource,"PUT")
    IF (this.lError)
       RETURN NULL
    ENDIF
    
    RETURN loResult
ENDFUNC

ENDDEFINE
```

To use this you would then just use:

```foxpro
loProxy = CREATEOBJECT("CustomerServiceClient")

loResources = loProxy.GetResources()
if ISNULL(loResources)
   ? loProxy.cErrorMsg
   RETURN
ENDIF   

? loResources.Count
? loResources.Item(0).Locale
? loResources.Item(0).Text


loResource = GetMyResourceToUpdateFromSomewhere()
loResource = loProxy.UpdateResource(loResource)

if !llResult
   ? loProxy.cErrorMsg
   RETURN
ENDIF   

? loResource.ResourceId
? loResource.Locale
? loResource.Text
```

This latter approach is preferrable as it treats service access like a business object where all logic created around the service is created in one place. It allows you to reuse the service calls and isolate your error handling and future maintenance all in one place.