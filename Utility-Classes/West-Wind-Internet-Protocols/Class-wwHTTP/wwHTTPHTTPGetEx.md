This low level method is the full featured HTTP access method that provides full support for the HTTP protocol including POST data, Basic Authentication and HTTPS protocol support. Requires `HTTPConnect()` to open a connection first and HTTPClose() to shut down.

**Meant for internal use**.

> **Note: Use `Get()`, `Post()`, `Put()`, `Delete()` or `HttpGet()` instead!**  
> These high level functions provide all the functionality of these low level methods without all the setup and multiple method calls - there is little to no need to use them in application code - they are for internal use primarily and implement the higher level features.