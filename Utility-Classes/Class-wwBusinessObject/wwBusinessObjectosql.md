An instance of a wwSQL object that handles data connection if nDatamode = 2. All SQL commands run through this object for remote data.

This object is set only afer calling SetSQLObject() or Open(). When Open() is used to create a connection it creates a new oSQL instance and uses the cConnectionString property to open the connection.