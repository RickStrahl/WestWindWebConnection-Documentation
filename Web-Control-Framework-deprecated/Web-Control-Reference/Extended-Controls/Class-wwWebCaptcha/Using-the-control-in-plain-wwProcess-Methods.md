The wwWebCaptcha control is easiest to use in Web Control Framework pages because it automatically handles creation of the Captcha and validating it. All your code has to do is check the IsValidated property at the right time.

However, you can also use the control in plain Process method code (remember that Session state is required):

```foxpro
FUNCTION CaptchaValidation
lcResult = ""

Captcha = CREATEOBJECT("wwWebCaptcha")
Captcha.Id = "Captcha"
Captcha.CaptchaChars = "ABCDEF123456789"
Captcha.FontSize = 32
Captcha.FontName = "Comic Sans MS"

IF Request.IsPostBack()
	IF Captcha.ValidateCaptcha()  
	   lcResult = "Thank you for correctly entering the Captcha code!"
	ELSE
	   lcResult = "<span class='errormessage'>I don't think so! Invalid Captcha Code. Try again...</span>"
	ENDIF		
ENDIF

* ** We have to submit the captcha AFTER checking 
Captcha.SubmitCaptcha()
lcCaptchaHtml = Captcha.Render()

TEXT TO lcHTML TEXTMERGE NOSHOW
<form method="POST" action="">
Please validate this request:<br>
< lcCaptchaHtml >
<input type="submit"  name="btnSubmit" value="save">
</form>
<hr />
<lcResult>
ENDTEXT

this.StandardPage("Captcha User Validation",lcHtml)
ENDFUNC
*  CaptchaValidation ::  CaptchaValidation
```