Executes a SQL statement that doesn't return a cursor result. This method is more efficient than Execute when dealing with INSERT/UPDATE and Stored procedures that do not return data.

For wwAdoSql this method is required for calling Stored Procedures that don't return cursors and have OUT parameters. If you use Execute instead of ExecuteNonQuery() the OUT parameter will not be returned.