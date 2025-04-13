If you have been using wwIPstuff to send emails updating to this component is very quick and easy. To get maximum compatibility with the old component running wwSmtp in classic mode change the following:

```foxpro
SET CLASSLIB to wwIPSTuff additive
loIp = CREATEOBJECT("wwIPStuff")
```

to:

```foxpro
DO wwSmtp
loIp = CREATEOBJECT("wwSmtp", 2)   &&  nMailMode = 2 is classic mode
* loIP.nMailMode = 2    && also works instead of Init parameter
```

The remainder of property assignments and SendMail() calls should work the same. If you were passing parameters to the SendMail() call rather than setting properties of the wwIPStuff/wwSmtp component, you will have to adjust the code to use the property interface as the various Sendxxx() methods no longer support parameter inputs. All values are read of the property interface.

### Using .NET Mode
The example above demonstrates how to use the classic wwIPStuff mail mode using a Win32 DLL which is most compatible with existing code. 

If you want to use the .NET component to provide more reliable SMTP messaging and support for features like SSL/TLS encryption, better multi-message sending, and support for embedded images in messages, you can switch nMailMode to 0 or don't pass a parameter to the CREATEOBJECT() call as .NET mode is the default. Most basic mail sending operations perform identically in both modes, with only additional features (like SSL and AlternateView support) added in .NET mode. 

Keep in mind that .NET mode requires at least .NET 2.0 installed on the machine.