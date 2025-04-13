Method that generates an RssFeed as an HTML based element list. 


```foxpro
loConfig = CREATEOBJECT("HtmlRssFeedConfig")
loConfig.FeedUrl = "http://feeds.feedburner.com/rickstrahl"

lcOutput = HtmlRssFeed(loConfig)
this.AssertTrue(!EMPTY(lcOutput),"Feed should return HTML result")

this.MessageOut(lcOutput)
```