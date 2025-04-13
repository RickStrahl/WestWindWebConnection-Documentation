A collection of Validation Errors that let you indicate errors that occured during calls to the Validate() method or otherwise.

Each Validation error is an instance of wwValidationError object which has the following structure:

**cMessage**  
Error Message

**cObjectName**  
Optional - The name of the control that the error occurred on. This flag is used to allow the Page handler to automatically pick up the control and allow assigning of validation messages next to the control.

The best way to create ValidationErrors is to use the [AddValidationError()](vfps://Topic/_1LM13YW8O) method of this class. 

The Validation Error Collection can be added the wwWebPage.BindingErrors Collection using the wwWebPage::AddValidationErrorsToBindingErrors() method which allows displaying both validation and binding error messages in a single error display.