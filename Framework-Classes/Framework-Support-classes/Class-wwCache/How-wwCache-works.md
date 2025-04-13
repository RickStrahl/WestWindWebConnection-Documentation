The wwCache object provides a very simple caching mechanism to hang on to string based output that was previously generated. You can deposit this output in the cache to avoid re-generating that same content again. Cache items have a key and an expiration and you can easily check for existance of cache content and retrieve it if it exists.

In Web Connection the cache object is instantiated on the the Server object and is always available as:

```foxpro
Server.oCache
```

in process requests.

A typical usage scenario is a static list in an application. For example, in our Web store the category list rarely changes so the list can be generated once and then be cached and reused from the cache. The code to do this might be as simple as this:

```foxpro
FUNCTION Categorylist
LOCAL lcOutput, oLookups

*** See if the output is cached
lcOutput = Server.oCache.GetItem("wwstore_categorylist")
IF !ISNULL(lcOutput)
   RETURN lcOutput
ENDIF

oLookups = CREATE([WWS_CLASS_LOOKUPS])
#IF WWSTORE_USE_SQL_TABLES
oLookups.SetSQLObject(Server.owwStoreSQL)
#ENDIF

oLookups.GetCategories("_TLookups",,.T.)

*** Build a simple string outptu from the categories
*** HTML includes specific HTML formatting for hover
*** buttons and underline adding to the look and feel
lcOutput = ""

SCAN
 lcoutput = lcOutput + ;
   [<tr><td width=100% align="right" class="menulink">] +;
   [<a href="itemlist.wws?Category=] + UrlEncode(TRIM(cData1)) + ;
   [" class="menulink">]+TRIM(cData1) + [</a></td></tr>]+ CRLF + ;
   [<tr><td height="1"><img src="space.gif" width="100%" height="1"></td></tr>] + CRLF
ENDSCAN

*** Add the generated HTML to the Cache
Server.oCache.AddItem("wwstore_categorylist",lcOutput)

USE IN _TLOOKUPS

RETURN lcOutput
```

In this scenario the cache content avoids a trip to the database and creation of the HTML.

### Using a Fixed Table for Caching
By default wwCache uses a Cursor for caching which means that each individual instance of FoxPro has its own copy of the Cache. This means that there may be cache duplication if you are running multiple instances. Using a cursor is a good choice as it avoids issues of memo bloat and index corruption of tables as the cache tables get recreated on every startup.

In most situations cursors work fine - caching works best in high volume scenarios and not for one of requests that might get run a couple of times, so re-running requests is usually not an issue. However, if you have situations where the cache content is required to be in sync and if you have long running requests that create cache content it might make more sense to have shared Cache state between instances.

To do this you can set the `wwCache` class `cFixedFileName` property to the name of a Table stored on disk. the table will be automatically created. The best way to accomplish this is to create a custom subclass of the wwCache class:

```foxpro
DEFINE CLASS MyCache AS wwCache
cFixedFilename = "__wwCache"
ENDDEFINE
```

and then tell Web Connection to use your custom class in WCONNECT_OVERRIDE.H:

```foxpro
#UNDEFINE WWC_CACHE 
#DEFINE WWC_CACHE             MyCache
```

At this point you can continue to use `Server.oCache` and get your persistent file based cache. 

### Multiple Cache Entries for the same page
In many situations you may have to cache multiple pages based on the querystring parameters passed. For example, you may have an RSS feed that has no parameters, and more specific feeds that return data for a specific subset and each of these should be cached.

You can omit the lcKey parameter on all of the Cache methods to automatically create a key that is specific to the URL entered, so that you can in effect cache multiple version of a page. So for example, on the Message Board RSS feed you can cache the default cache feed, and the cache feed for just the Web Connection Forum, and the feed for the Internet Protocols etc. etc.

### Table based caching
The wwCache class by default uses Cursors, which means that each instance of Web Connection uses a separate cache cursor. However, you can specify to use a fixed table by setting the cFixedFileName property which results in a sharable table that can be used by all instances simultaneously. The advantage is that you have a common cache pool and all are seeing exactly the same results. For very busy sites it's recommended that you do use a shared table to minimize cache incoherency (mismatched between caches in different instances).

Note that if you use a table based cache file you will need to pack and reindex the file occasionally as the file can quickly get large! Make sure you do this occasionally to avoid memo bloat and index corruption and keep the cache size manageable and performing fast.