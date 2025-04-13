When building database Web applications, security is important. Confidential data might be traveling over the wire and You wouldn't want to capture orders online, including credit card numbers, and then have somebody hijack the entire order/customer file with that sensitive information. Security comes in many flavors and applies to different aspects of a Web site. Is the information passed over the Web safe? Are the resources on your server secure from outside access? How do you keep people from accessing certain parts of your application? Are passwords kept secure across the network? 

Windows provides excellent, though somewhat complex, security features that should address the majority of your security needs for server resources. Recent versions of Windows are secure and locked down by default and you have to explicitly enable features and open up ports in your firewall explicitly to allow access to any resources on the server.

Web applications also require application level security, which needs to be implemented at the application. Web Connection provides basic security features out of the box 


You can enforce security through several mechanisms on Windows Web systems:

* **[HTTPS/SSL](vfps://Topic/_S8W0RU73O)**  
provides encryption of content travelling over the wire. 

* **[Basic Authentication](vfps://Topic/_S8W0RUIBB)**  
code based mechanism to force users to log in before accessing your application code. 

* **[NT File & Directory Security](vfps://Topic/_S8W0RUUYY)**  
file based security to keep people from accessing files on your server.

* **[Web Connection Installation Security](vfps://Topic/_0J40WN2LX)**  
Finally you also need to secure your Web Connection application. There are a few files that are exposed and should be configured for appropriate security.