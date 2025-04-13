Returns routing info and Path Segments when dealing with extensionless URLs.

Method returns an object with the following properties:

* **lIsRouted**  
If true means this request was re-rewritten by the server and routed.

* **cOrignalUrl**  
When a request is routed the URL is rewritten to a new URL which is reflected in the Server Variables for the request. This property returns the original URL the user requested. This is the full request URL

* **cOriginalPath**  
Similar to cOriginalUrl but returns the logical server relative Web path.

* **oPathSegments**  
Contains each of the segments between the slashes for this path request. For example the path: 
  
/api/album/2332  
  
has 3 segments which are expressed in the collection. To retrieve the album id you can use code like the following:

```foxpro
loRouteInfo = Request.GetRoutingInfo()
lcCustId = loRouteInfo.oPathSegments.Item(3)
```

Note that if you override the OnUrlRewrite() method you should create and save the RouteInfo structure as a property to make it available to the Process methods that might be called.