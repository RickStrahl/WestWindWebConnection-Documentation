Username that can be used for HTTP authentication that requires username and password.

Authentication Schemes supported are:

* Windows/NTLM/Negotiate Authentication
* Basic Authentication
* Digest Authentication

> #### @icon-warning HTTP Challenge Required for Authentication Headers to be sent
> **WinInet does not send credentials on the initial request**, but rather requires a server challenge (`401 UnAuthorized`) response first, to determine which auth mechanism to use. This may cause problems with services and servers that don't challenge which is not uncommon especially for **Basic Authentication** of REST style services.
>
> If you need to pre-authenticate for **Basic Authentication** and send credentials on the initial request, you can use the [AddBasicAuthenticationHeader()](VFPS://Topic/_6BD0TL38Q) method which applies the `Authorization` header directly to the request and sends it on every request you apply it to immediately.