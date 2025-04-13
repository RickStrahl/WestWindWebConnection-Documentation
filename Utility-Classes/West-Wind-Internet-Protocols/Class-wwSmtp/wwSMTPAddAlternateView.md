Allows adding an alternate view of the message that provides richer content. For example you may configure the main message as plain text and the alternate view as html including embedded images that are stored as part of the message. 

This feature works only when `nMailMode = 0` (.NET mode). If all you want is to create a plain text AND HTML message you can use the [cAlternateText](vfps://Topic/_2QL0TV6M3) and [cAlternateContentType](vfps://Topic/_2QL0TV6M5) which works in both modes. 

AlternetViews should be used only if you need to create more than two different alternate views, or if you want to embed images directly into the message content itself for self-contained image messages without external links.

### Creating an alternate HTML View with Embedded Images
For example the following creates a simple message that has only a text marker for non-HTML clients and an HTML alternate view that contains a rich message and an embedded image to display in the message:

```foxpro
loSmtp.cSubject = "Test Message through wwIPstuff"
loSmtp.cMessage = "This message contains rich text content as an alternate view"
loSmtp.cContentType = "text/plain"

LOCAL loAlternateView as wwSmtpAlternateView
loAlternateView = CREATEOBJECT("wwSmtpAlternateView")
loAlternateView.cText = "<b>Hello</b> World! <img src='cid:sailbig' />"
loAlternateView.cContentType = "text/html"
loAlternateView.AddLinkedResource("c:\sailbig.jpg","image/jpeg","sailbig")
loSmtp.AddAlternateView( loAlternateView )
```

In this scenario any mail client that does not understand the main content type (text/html) will display the alternate view which is just plain text.

### Embedded Linked Resources
Alternate views can also embed linked resources - that is resources that are embedded as part of the message and display inline of the message. By creating an wwSmtpAlternateView class and setting cText, cContentType on this object and then adding a linked resource this linked resource can be embedded directly into the message. 

Linked Resources are added with a file path, a MIME content type and a resource Id (in this case 'sailbig'). This resource id can then be embedded and linked from the message text by using:

```html
<img src="cid:sailbig"  />
```

inside of the message text.