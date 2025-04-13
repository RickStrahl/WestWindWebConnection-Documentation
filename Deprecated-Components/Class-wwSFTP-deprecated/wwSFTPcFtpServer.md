Specifies the FTP Server to connect to.

This method is used to make a connection to the FTP server for the lower level functions. Once a connection is open you can perform multiple FTP operations on that connection, until you call `FtpClose()`.

> #### @icon-warning Domain Name and IP Addresses only
> The value specified in this property is either a domain name (mysite.com) or IP address (179.155.155.5), **not a full URL**. 

**Default value**: 23