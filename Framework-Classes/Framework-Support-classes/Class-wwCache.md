The wwCache class provides a simple caching mechanisms for strings. Caching allows you to generate output once, store it to the cache and then attempt to retrieve it from the cache if it already exists the next time the same content is required. This can save time reproducing output that might be very time consuming and instead simply reusing previously generated output.

Cached items are accessible by key and have an expiration, which allows for timing the availability of cached content to a timeout period that content is valid. If you have a busy site, don't be afraid of short cache durations. It's better to generate once and have an item served hundreds of times in a few minutes, than generate each and every time. The savings are huge and the overhead of re-generating every few minutes is likely minor by comparison. 

Caching is powerful for storing previously generated output - complete HTML repsonses, or HTML fragments of a page. XML results or fragments of results. You can even cache binary content like the result of a PDF output generation for example. Anything that can be represented as a string can be cached.

Caching can provide huge performance gains for your applications by avoiding regenerating output and not requiring you to go back to the database to re-run complex queries. Instead results are returned from a single indexed key of the cache cursor or table. 

```foxpro
DO wwCache

*** Generally try to make the cache 'global' so it sticks around
goCache = CREATEOBJECT("wwCache")

*** Check if the key exists
lcTime = goCache.GetItem("Time")

*** If it doesn't add a new key
IF ISNULL(lcTime)
	goCache.AddItem("Time", TIME() )
ENDIF

*** Retrieval from cache at some point
? goCache.GetItem("Time")


*** Get a key or add one if it doesn't exist
*** This code effectively does what the code above does in 1 line
lcTime = goCache.GetOrAddItem("Time", Time())
? lcTime   && Existing or new value
```