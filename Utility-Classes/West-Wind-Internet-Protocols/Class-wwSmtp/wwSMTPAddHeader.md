Adds an SMTP header to this email request using a key value pair.

Many common headers are settable through properties of this object directly, but if you need to add specific headers you can use this method to do so. Please check the properties for a matching header first as the property settings will override any custom headers you add manually with this method.

```foxpro
loSmtp.AddHeader("Return-Path","bounce@mydomain.com")
```