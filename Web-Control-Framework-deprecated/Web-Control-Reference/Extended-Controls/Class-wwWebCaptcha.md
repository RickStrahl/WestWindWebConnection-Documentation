The CAPTCHA control can be used on a form to validate user input to increase the likelyhood that the page is filled by a live human as opposed to a robot or HTTP client. The control displays an image with a code that needs to be manually typed in to match and validate the request.

![](IMAGES%5CWebControls%5CControls%5CwwWebCaptcha.png)

Using the control is as easy as dropping the control on a form, setting up the characters that it should display and a length of the string and font infomration. The control will then render the image for you as a separately loaded image. The image and text is rendered automatically.

To validate the input of the control you can check the IsValidated property which is true only if the user entered the correct code. Based on the result you can accept or fail the form submission.