A special collection that allows key and value pairs to be stored and retrieved by the key name or index. 

This class manages the Collection in a two dimensional array aItems which contains key and value in the first and second columns respectively.

```foxpro
loCol = CREATEOBJECT("wwNameValueCollection")
loCol.Add("Item1","This is Item1")
loCol.Add("Item2","This is Item2")

? loCol.Count              && 2
? loCol.Item("Item2")      && This is Item2
? loCol.Item("Item1")      && This is Item1

? loCol.aItems["Item2",2]      && This is Item1
? loCol.aItems["Item1",2]      && This is Item2


? loCol.Item("ItemBOGUS")  && .NULL.

? loCol.GetIndex("Item2")  && 2
? loCol.GetKey(2)          && Item2

loCol.Remove("Item1")
? loCol.Count              && 1
```