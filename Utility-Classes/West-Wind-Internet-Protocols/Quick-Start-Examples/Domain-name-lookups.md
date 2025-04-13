You can easily resolve domain names from IP addresses and IP addresses from domain names. This may be useful for tracking user access and backtracing the client IP addresses coming into your server for logging or security purposes.

The methods are a easy to use and require access to the Internet and a DNS server which must be configure in your TCP/IP settings for the machine you're operating.

```foxpro
DO wwAPI && Load library

*** Domain Lookups from IP Address and Reverse Domain Lookups
? GetDomainFromIp("205.162.195.2")
? GetIpFromDomain("ftp.west-wind.com")
```