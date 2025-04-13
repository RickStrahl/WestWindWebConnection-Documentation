Web Connection supports creation of SQL Server tables to hold logging, session and async request information. Using SQL Server can be useful for systems that are otherwise using SQL Server for data access, to remove any dependencies to local VFP tables. This may be especially important for large installations that are running on load balanced systems where multiple servers must share these session and log files.

To add SQL server sessions and logs you need to:

* Set `WWC_USE_SQL_SYSTEMFILES` compiler flag in `wonnect_override.h`
* Create the SQL Server tables in a database
* Set a Connection string in `YourApp.ini` and the `SqlConnection` key


### The Create SQL Server Tables Wizard
The **Create SQL Server Tables Wizard** in the Web Connection Console takes you through creating a database or using an existing one to add the `wwRequestLog` and `wwSession` table to. Once the tables are created additional manual steps are required to actually configure the Web Connection application to use those tables, which is described in the last steps of this topic tree.

> #### @icon-info-circle Not available to Shareware Version Users
>This feature is not available for shareware version users, since the implementation requires a recompile of the classes. You can create the databases, but the actual tables will never be accessed or read from.

### Use Westwind.sql
Note that you don't have to run the Wizard described in this walkthrough. You can create the database using the script that can be found at:

```txt
\Templates\Westwind.sql
```

To do this:

* Open SQL Server Management Studio (or whatever tool you like to use for SQL)
* Open `Westwind.sql` and copy the SQL text
* Select or create a database
* In SSMS open a new Query Window in the database you want to add the West Wind Tables
* Paste the SQL script and execute

What is created:

* WebRequestLog Table
* wwSession Table
* Two stored procedures for wwSession to add and retrieve a session value