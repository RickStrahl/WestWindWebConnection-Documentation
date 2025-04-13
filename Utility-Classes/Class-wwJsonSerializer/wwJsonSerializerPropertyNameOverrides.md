Comma delimited list of property names to override in the result JSON document. 

This property allows you to easily transform one or more JSON properties by overriding their character character casing. JavaScript and JSON are case-sensitive, but FoxPro cannot easily determine case from a property/value/field, and **wwJsonSerializer by default only generates lower case property names**. This properties let you specify properly cased property names and apply them to the JSON document.

To use, you specify a comma delimited list of properly cased names that need to be not-only-lowercase by specifying the properly cased name. The original lower case names are are then replaced with the properly cased names in the entire result JSON document. 

This operation works on the result JSON string, by looking up each property in an case-insensitive manner, and replacing each property with the case-sensitive value you provided.

> #### @icon-info-circle Works on the Entire JSON Document
> The property overrides are applied through the **entire JSON document** and JSON object hierarchy, so it updates JSON properties in top level objects, child objects and child arrays recursively.

> #### @icon-info-circle Completely re-map a Property Name with MapPropertyName()
> If you need more naming control, or need to map a property to a new name that changes more than just case, you can map an individual property using [MapPropertyName()](VFPS://Topic/_67X0KWDFD). This method also allows mapping to names that are not valid FoxPro property names including those that include spaces and special characters.