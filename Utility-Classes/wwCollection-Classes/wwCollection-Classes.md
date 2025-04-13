Web Connection and the Client Tools include a set of Collection classes that are implemented via arrays. Unlike the limited FoxPro Collection class, these classes provide a richer interface, much faster operation (roughly 3x faster) and more consistent operation that you would expect from collection classes. 

The only downsides to these classes is that you can't use `FOREACH` to walk through the items and you can't use Indexer syntax (`loCol[x]`) - you need to use the Item() method or access the indexer on the raw `loCol.aItems[x]` to step through the classes. 

<%=  ChildTopicsTableHtml(oHelp,'CLASS','Class',,'')%>