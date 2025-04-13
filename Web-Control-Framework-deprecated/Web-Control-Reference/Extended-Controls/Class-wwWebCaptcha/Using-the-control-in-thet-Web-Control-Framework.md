Use of the control is super simple. To use it:
<ul>
* Ensure that Session state is enabled by setting EnablePageState=.T. in the Page class definition
* Drop the control on the form
* Set properties to control how the control displays
* Implement your form's submit code to check the IsValidated property
</ul>

### Ensuring that Session State is enabled
The control uses Session state to communicate user specific Captcha values so Session state must be enabled on the page or the process class as a whole. If you're doing this at the Process level Process.InitSession() should be called in OnProcessInit of the Process class. If you're using a page specific setting for Session state use the following in your generated class (abstracted):

```foxpro
DEFINE CLASS MyPage as wwWebPage

EnablePageState = .T.

FUNCTION OnLoad()
...
ENDFUNC

ENDDEFINE
```

### Drop the control and set properties
You can simply stick the control on the page and assign various properties. The control has default values set so you don't need to set anything, but you can customize how the control displays.

```html
<ww:wwWebCaptcha runat="server" id="Captcha" 
         CaptchaChars="ABCDEF123456789"
         FontName="Comic Sans MS" FontSize="28" ></ww:wwWebCaptcha>
```

### Implement your form's submission code
Typically you'll want to handle Captcha submissions in a button click event such as clicking on a Save button of a form. All you need to do is check the IsValidated property:

```foxpro
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
* ShowEntry_Page :: btnPostComment_Click
* *** *** *** *** *** *** *** *** *** *** *** *** ***
FUNCTION btnPostComment_Click()

* ** Create empty comment
this.oComment.New()

* ** Unbind data from the form
this.Unbind()

IF THIS.BindingErrors.Count > 0
   this.ErrorDisplay.Text = this.BindingErrors.ToHtml()
   RETURN
ENDIF

IF !this.Captcha.IsValidated
	this.ErrorDisplay.ShowError("Invalid Validation Code")
	this.SetFocus(this.txtComment)
	RETURN
ENDIF

...

* ** Eventually save the comment
this.oComment.Save();

ENDFUNC
```