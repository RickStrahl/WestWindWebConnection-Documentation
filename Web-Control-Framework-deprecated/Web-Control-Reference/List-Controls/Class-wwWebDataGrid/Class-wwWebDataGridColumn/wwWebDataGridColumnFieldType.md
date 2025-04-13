Explicit declaration of the FieldType for the resulting expression.

This value is optional for most expressions, as Web Connection figures out the type at runtime.  However, it's important to provide type information for complex expressions calling methods/function since TYPE() cannot return type information on those.

If TYPE() returns 'U', Web Connection defaults to string, which may or may not work, but is the most common scenario.