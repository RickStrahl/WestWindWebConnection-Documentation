Opens a connection to the server. Actually it doesn't open the connection, but it sets up the connection properties. Actual connection occurs when a send or receive operation on the FTP connection.

All parameters to this method are options - you can also set the related properties on the **wwSftp** object (`.cFtpServer`, `.cUsername`, `.cPassword`) which is the more common use case (see example).