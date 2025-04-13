Flag that can be set to cause the Post Buffer to use a File Stream to hold POST data. This allows the buffer to be larger than 16 megs to send large content to the server.

```foxpro
DO wwHttp
loHttp = CREATEOBJECT("wwHttp")

*** Allow for greater than 16mb
loHttp.lUseLargePostBuffer = .t.

loHttp.nHttpPostMode = 2  && multi-part forms

loHttp.AddPostKey("title","Some Large Picture")
loHttp.AddPostKey("image","c:\temp\VeryLargeImage.tif")

loHttp.Send("https://somesite.com/uploadFiles")
```