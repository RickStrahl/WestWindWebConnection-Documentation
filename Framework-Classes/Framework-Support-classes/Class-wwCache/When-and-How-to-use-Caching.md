Caching is a great technology feature, but you want to be sure to understand what it implies. Caching is used to improve performance of repeated requests that are essentially semi-static - they get created once and then then don't need to be refreshed dynamically for some period of time.

Typical examples of cachable content include things like RSS feed, Product category pages in an E-Store, News pages, Home Pages in dynamic Web sites etc.

Caching is most efficient for busy sites where many hits can be saved by using Caching. 

You should be careful to cache content for too long of a period. By doing so you making it more difficult to refresh content once it is loaded into the cache. The longer the cache expiration period the more concern there is about stale content being served.  That said you should realize that on a busy site short cache times can reap enormous benefits. If you have a site that is getting 10 hits a second to a specific page and you cache this page for a mere 1 minute you are serving this page 600 times for actually generating the text for it once. Increasing the cache expiration will not improve the performance of this operation (unless it is extremely lengthy) by much. Keeping cache expiration as short as possible should be considered.

### Post Cache Replacement
The wwCache class returns results as strings, which means you also have the possibility of updating the Cache content once it's been retrieved from the cache. If you have a page that is 90% cacheable but contains a small block of updatable content (ie. a shopping cart summary in an E-Store) you can leave a placeholder in the cached content and perform a STRTRAN() on that data for quick post-cache substitution. This is a very powerful feature!

### II6 and later HTTP Caching
If you're using IIS 6 you can also take advantage of Web Connection's support for Kernel Caching. Kernel Caching allows a page to be cached by IIS so it never actually hits your Web Connection backend while cached. To do this you can use the [wwHttpHeader::AddCacheHeader()](vfps://Topic/_1M51BWMHJ) method to apply maximum caching for the request:

```foxpro
LOCAL loHttpHeader as wwHttpHeader
loHttpHeader = CREATEOBJECT("wwHttpHeader")
loHttpHeader.SetProtocol()
loHttpHeader.Setcontenttype("text/xml")
loHttpHeader.AddCacheHeader(300)  && 5 minutes

Response.Write( loHttpHeader.GetOutput() )
Response.Write( STRCONV(lcXML,9) )
```

If you use this option be sure that your caching can be full page caching as your Web Connection request will not get hit again.