Provides a quick way to set a Locale format for a request. Locale information determines date and number/currency formats.

This method sets the Locale to the browser's Locale provided via the [Request.GetLocale()](vfps://Topic/wwRequest%3A%3AGetLocale) method. 

If this method is called the wwServer::oLocaleInfo is available which provides detailed info on the locale in use. It's based on the wwLocaleInfo class.

You can call this method with an optional value of .T. to reset the Locale back to its default locale which is specifed in the wwServer::cDefaultLocale property. 

The Currency Symbol can be overridden via the wwServer::cLocaleCurrencySymbol. If this value is non-blank the specified currency symbol is used regardless of the Locale in use. This is useful if your site always accepts payments in a single currency, but still wants to display numbers in the appropriate number format to the browser.