This method creates a new session table if one doesn't exist yet or if you want to create a new one manually. This method is called automatically when a session is accessed.

The Session table is created with the following structure:

```sql
CREATE TABLE ( THIS.cDataPath+THIS.cTableName ) FREE;
  (SESSIONID    C (14),;
   USERID      C (15),;
   FIRSTON     T ,;
   LASTON      T ,;
   VARS        M ,;
   BROWSER     M ,;
   IP          M ,;
   HITS		   I )
 )
```

You can add additional fields to this table for high frequency values that are set - direct access to fields is more efficient than session variables especially if you need to query information later on for statistical reports. Any fields you add are accessible with the [SetField()](vfps://Topic/wwSession%3A%3ASetField) and [GetField()](vfps://Topic/wwSession%3A%3AGetField) methods.