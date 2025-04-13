A Captcha like control meant to verify that a human is posting to this link rather than an automated bot. Use on publically accessible forms that aren't secured behind authentication.

This control uses a simple math expression instead of an image to verify a human is accessing a form. 

![](IMAGES%2FWebControls%2FControls%2FMathCapture.png)

The control asks for a simple math expression using addition and multiplication to provide validation. The control renders the expression. User input can be validated on the server using the IsValidated property when the form is submitted.

To use and embed the control into a page::
```html
<ww:wwwebmathcaptcha id="Captcha" runat="server" 
      CssClass="notebox" 
      MaxMathNumber="5" />
```

To check for a valid response in user code - like a Save button click:

```foxpro
IF (**!this.Captcha.IsValidated**)
   this.AddBindingError("Invalid validation.","Captcha_Input")
ENDIF
```