Returns a value first by checking the Form variable in the Request buffer, and if it doesn't exist (or the request isn't POSTBACK) returning the value.

Use this function to get data depending on whether the request is a postback operation or not. On first hit use the model to drive the value. On subsequent hits when data has been entered use the form data instead.

This is especially useful in Scripts and templates where you need to bind the `value=""` property using **< %= % >** expressions to ensure that you get the post back values displayed when binding errors occur.