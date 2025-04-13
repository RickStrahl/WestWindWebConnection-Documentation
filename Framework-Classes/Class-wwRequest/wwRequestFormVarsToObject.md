Parses an object and pulls form variables from the request buffer by matching the property names to the request variables.

> In v7.0 and later, this function replaces the `.aFormVars()` method of older versions.

Optionally you can pass in a prefix for form variables to allow for potential naming problems for properties of multiple objects.

Ideally you pass in existing object and Web Connection will populate the properties of the object from the form variables doing type conversions to match the type of the property assigned to. 

If you don't pass an object a generic object is created for you with properties for each of the form variables of the HTML form and populated with String values only.

This method can be extremely useful for reducing form variable retrieval code from your Web applications especially if you map form fields directly to objects. Form variables must match the property names either directly or with an optional prefix (such as txt, but the prefix must consistent for all variables). You can call this function multiple times with different prefixes to map multiple objects.