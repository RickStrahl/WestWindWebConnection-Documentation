***Tracking users through a site by keeping state information in a Session table.***

wwSession provides a server centric user state tracking mechanism that is backed by generic data storage mechanism. Essentially you tie an Id (typically an HTTP Cookie) to a Session record which can contain any number of values that are stored in the session table as XML encoded data.

A session object allows your application to maintain a visitors state throughout a visit through your site. Through the unique ID that identifies this user you can store user specific information in the session store and retrieve on subsequent hit. You can think of this object of a generic data record for the user without having to create a specific table and field for each value to store temporarily.

Session can automatically be enabled in Web Connection using [wwProcess::InitSession()](vfps://Topic/_S8413MYP2) which enables session management for one or all requests depending on where it is called.

wwSession uses a table to track user session information and can use either Fox tables or SQL server. Because of the table format Web Connection Sessions can work across machines although SQL Server sessions are recommended for this scenario for performance reasons.