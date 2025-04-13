If .T. requires accounts to be validated before they can be accessed. 

Internally this sets the `Active` flag to `.F.`, sets a `Validate` unqiue key value and requires that you validate the account before you can access it. 

The default value is `.F.`.

> **Note:** There's no built-in mechanism to provide for this validation - it's up to your application to provide the validation workflow. Typical flow: Send an email with a link to validate the validation key, and if valid flip the `Active` flag and clear the `Validate` field.