wwSession uses FoxPro tables to run the session management features. A subclass - wwSessionSQL - uses SQL Server tables underneath everything. To use the wwSessionSQL class set the oSQL member to a reference of a wwSQL object. 

```foxpro
oSQL = CREATEOBJECT("wwSQL")
oSQL.Connect("server=.;database=webstore;")  

oSession = CREATEOBJECT("wwSessionSQL")
oSession.oSQL = oSQL
```

From thereon in the session behaves identically to a session running against Fox tables.

Alternately SQL Sessions can be established through the InitSession mechanism by telling Web Connection to use SQL Server tables for logging and Session support. See [Configuring Web Connection for use with SQL Server](vfps://Topic/Configuring%20Web%20Connection%20for%20use%20with%20SQL%20Server) for details on how to set up the tables and let Web Connection handle logging and session management into SQL Server tables.    

wwProcess::InitSession automatically loads the correct session object based on the WCONNECT.H constant WWC_USE_SQL_SYSTEMFILES.