Generic, self-contained response for Authentication (HTTP 401 Status) by the server. 

By default this creates a bare `Status 401` request without any special protocol headers which forces the Web Server to request authentication for the client. This default is used by application based security solutions, such as **User Security Authentication**.

If `llBasicAuth` and/or `llWindowsAuth` are passed as true, the appropriate headers are added for that **Basic Authentication** or **Windows Authentication** respectively. You can use both in combination.

> This request only **generates a request for Authentication** - it doesn't handle authentication flow or validation of users. If you need the actual Authentication functionality look into  [wwProcess::Authenticate()](VFPS://Topic/_1P10UVIG9).