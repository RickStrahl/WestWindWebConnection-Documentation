Dual purpose function that is used to set a CAPTCHA image and then also serve the image to the client. CAPTCHA can be used to validate a request and ensure that a user enters the data as opposed to a robot or HTTP client to minimize SPAMming any form entries.

Please also check out the wwWebCaptcha Control which makes use of Captcha in Web Control Framework pages very easy.

The first mode allows setting up the CAPTCHA with text and font and font size information. This sets up the CAPTCHA and stores the information in the active Session object. Note InitSession() must have been called! The session Variable used is "__Captcha".

The second mode is responsible for actually serving the CAPTCHA to the client by using a special URL in yoru process class:
<ul>
* wc.dll?YourProcess~CaptchaImage
* CaptchaImage.wwd
</ul>
where.wwd is the name of your scriptmap. This mode actually serves the image.

The easiest way to see this work is in a Process method that handles both display and validation of the CAPTCHA:
```foxpro
FUNCTION CaptchaTest
lcResult = ""

* ** On a Postback validate CAPTCHA against form input
IF Request.IsPostBack()
	lcCaptcha = Session.GetSessionVar("__CAPTCHA")
	IF LOWER(lcCaptcha) == LOWER(Request.Form("txtCaptcha"))
	   lcResult = "You matched it!"
	ELSE
	   lcResult = "Bing: Try again sucker"
	ENDIF		
ENDIF

* ** Generate a new CAPTHA  -  if you have a single page for display and postback
* ** make sure you generate the new CAPTCHA AFTER you've checked it!
this.CaptchaImage(RIGHT(SYS(2015),5),"Verdana",30)

* ** Notice the imagelink: CaptchaImage.wwd
TEXT TO lcHTML TEXTMERGE NOSHOW
<form method="POST" action="">
Here it is: <img src='CaptchaImage.wwd'> <input name="txtCaptcha" >
<input type="submit"  name="btnSubmit" value="save">
</form>
<lcResult>
ENDTEXT

this.StandardPage("Output",lcHtml)
ENDFUNC
```

You can also use the [wwWebCaptcha Control](vfps://Topic/_1WS0AI07W) which is easier to use than these low level functions, both in Web Control Pages (where everything is automatic) and in plain Process methods.