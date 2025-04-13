Internal flag used to determine whether to automatically clear any Named Parameters **after** a SQL command has executed, so the next command starts with a clean parameter list.

The `Execute()` or `ExecuteNonQuery()` methods set this flag to `.T.` after a command has executed, causing the next `AddParameter()` command to clear all existing parameters before adding the new one.

The purpose of this flag is **to override the default parameter clearing behavior** by doing the following:

Set the  `loSql.lParameterReset = .F.` **before the next call to `AddParameter()`**.