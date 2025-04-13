Updates a cursor from a collection by doing GATHER NAME MEMO for each member. An optional search expression can be provided for each item to allow updating existing records.

> It's important to note that **the result cursor or table has to exist and be open** in order to accurately determine the data schema to import to.

```foxpro
*** Create a collection from Data
select * from Customers INTO CURSOR TQuery
loData = CursorToCollection("TQuery")
close data

*** IMPORTANT: Writable table/cursor has to exist
SELECT * FROM Customers where .F. ;
         INTO CURSOR TImported READWRITE

*** Load data into a cursor from a collection
CollectionToCursor(loDataCollection,"TImported")
```