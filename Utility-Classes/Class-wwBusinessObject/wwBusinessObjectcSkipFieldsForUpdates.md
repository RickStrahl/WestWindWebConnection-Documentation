Property that holds fields that are skipped for Insert and Update statements built on the fly. Fields are specified as a comma delimited list.

Use this if your local object structure for record updates holds fields that are part of the SQL table, but shouldn't be updated on insert/update operations (ie. read-only values like Ids or initial values).

> If you use Identity keys  that are auto-generated in your database, you want to add your key field (like `Id` or `Pk`) to this list so they don't update the data which is likely to fail.