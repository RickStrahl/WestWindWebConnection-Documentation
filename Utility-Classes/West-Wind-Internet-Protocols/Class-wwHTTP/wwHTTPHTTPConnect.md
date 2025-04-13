This low level HTTP method initiates a low level HTTP request for use with HTTPGetEx by establishing an IP Session and connecting to a target server. The main purpose of this method is to specify the server name and the security options when connecting.

**Meant for internal use**.

> **Note: Use `Get()`, `Post()`, `Put()`, `Delete()` or `HttpGet()` instead!**  
> These high level functions provide all the functionality of these low level methods without all the setup and multiple method calls - there is little to no need to use them in application code - they are for internal use primarily and implement the higher level features.