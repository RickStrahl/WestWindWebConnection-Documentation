Determines if a secure connection using SSL/TLS should be used for this mail request.

Many mail servers today require secure connections, especially many of the online mail services most notable GMail, Outlook.com etc.

To use just set `lUseSSL = .T.`:

```foxpro
loSmtp.nMailMode = 0   && .NET Mode required - 0 is the default
loSmtp.cMailServer = "smtp.gmail.com:587"
loSmtp.lUseSsl = .T.

loSmtp.cUsername = "ricks25"
loSmtp.cPassword = "secret01"
```

You should only use this option if the mail server supports it. Check your ISP's mail service configuration options for your mail client to find out whether SSL or TLS is required for sending messages.