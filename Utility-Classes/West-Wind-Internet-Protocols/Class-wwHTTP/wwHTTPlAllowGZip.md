Determines whether wwHTTP requests and decodes GZip content. 

If set wwHttp sends the gzip Accept-Encoding header. If the server is capable of gzip it will return GZIP data to the client and wwHTTP will automatically decode the compressed data so operation of the class is not changed.