Determines whether wwHTTP requests accept and can decode GZip content. 

If set wwHttp sends the gzip Accept-Encoding header. If the server is capable of gzip it will return GZIP data to the client and wwHTTP will automatically decode the compressed data so operation of the class is not changed.

Behind the scenes this adds the following Accept header:

```http
Accept-Encoding: gzip
```

and checks result for `Content-Encoding: gzip` and if found automatically decodes it.