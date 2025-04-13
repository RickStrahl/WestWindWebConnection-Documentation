Executing objects safely by wrapping an error handler around any foreign execution.

This class can be used to execute code that is of unknown or variable such as code stored in memo fields and run later, or code stored in script pages and evaluated on the fly. These types of environments require a safety wrapper to make sure that any of this 'external' code doesn't crash the core application.

### How it works
The wwEval class implements methods that allow you to:

* Evaluate a FoxPro expression
* Execute a single FoxPro command
* Run a CodeBlock in fully interpreted mode at runtime (using CodeBlock)
* Merge a document containing expressions and code blocks (scripting)

All of these operations are implemented as methods of the wwEval class. The class contains an error method which traps these errors and records the error information. Upon return from the execution the calling code can check for errors by checking the lError flag and the cErrorMessage property.