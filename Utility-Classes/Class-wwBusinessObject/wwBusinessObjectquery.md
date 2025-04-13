High level query method that queries the underlying data store using `SELECT` statements. You can use partial SQL syntax (without using `INTO`) to retrieve query results that can be run on different SQL back ends.

For porting data between different data layers use this method for SQL query operations that can run against any backend.

This method can also automatically convert data into the `vResult` property, using a variety of different encoding formats based on the `lnResultMode` parameter:

* JSON
* XML
* Encoded DBF/Memo
* Collections
* ADO Recordsets