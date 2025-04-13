Once you've decided whether to add tables to an existing SQL Server database or whether to create a new database, you have to let the Wizard know how to connect to the database.

There are two connection options depending on how you want to create the database. In the previous step, if you chose:

* **Create a new Database**  
In this case you should provide the connection string to the Master database.

* **Add tables and Stored Procedures to an an existing database**  
In this case you should provide the connection string to the existing database.

![](///images/ManagementConsole/SQLConfig2.png)

If you used an existing database you can also use a DSN connection as long as the required login information is provided. If left out, you'll get a SQL server logon dialog. 

If you create a new database you'll want to connect to the master database since the new database won't exist yet. Once created the Wizard will connect to the new database.

Note, you can create the database on any server desired by specifying the Server= key in the connection string. The only requirement is that the login information is valid and allows to create the database and tables.