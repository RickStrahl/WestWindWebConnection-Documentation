Optional - Date when the cookie expires.

By default this value is empty which means the cookie expires when the browser is shut down.

Otherwise you can make the cookie persistent, by providing an expiration date or time.

```foxpro
loCookie.Expires = DATE() + 10
```