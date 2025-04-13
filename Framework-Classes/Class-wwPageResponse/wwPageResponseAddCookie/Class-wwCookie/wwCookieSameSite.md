Optional - Sets the `SameSite` flag.

Values:

* "Strict" - (default) cookies are sent only on the host site.
* "Lax" - cookies are sent to the host site and to any other site that requests the HTML page, but not related links (ie. images)
* "None" - cookies are sent to the host site and any other site that requests a resource. If you use this setting and are not setting the `Secure` cookie only the browser will give a warning or fail to send the cookie (not recommended any longer)
* Empty - no same site policy  (not recommended )
 
More info on SameSite cookie can be [found here](https://web.dev/samesite-cookies-explained/).