Used to set named parameters to be passed into SQL statements.

Note that must be defined in the same order as they appear in the SQL statement as native FoxPro parameters are injected as ? parameters, and ADO parameters are always treated sequentially.

For SQL Passthrough (wwSQL) this method has extra overhead - use FoxPro's native `?` parameter syntax instead. For ADO, this method is more efficient. It works for mechanisms so if you want to create universally usable code use this method.