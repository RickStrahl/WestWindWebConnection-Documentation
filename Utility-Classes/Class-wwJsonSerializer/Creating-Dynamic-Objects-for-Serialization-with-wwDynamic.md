If you need to create JSON structures to serialize from code, you can create FoxPro objects and then serialize them to JSON which ensures that the JSON values are properly serialized.

To make this task easier and more consistent you can use the `wwDynamic` class which allows creating objects at runtime using a more fluent syntax. `wwDynamic` allows to `just add properties` to an object to create it.

Take this JSON for example:

```json
{
    "name": "Rick"
    "company": "West Wind",
    "address": {
        "street": "10b Northwest Wind Houndage Rd",
        "city": "Paia"
        "state": "HI",
        "postalCode": "96779"
    }
}
```

To create this via FoxPro code:

```foxpro
DO wwJsonSerializer
DO wwDynamic


loCust = CREATEOBJECT("wwDynamic")

*** Properties are all created lower case in JSON
loCust.name = "Rick"
loCust.company = "West Wind"
loCust.address.street = "10b Houdage Road"
loCust.address.city = "Paia"

*** Dynamic objects support explicit case via
*** .AddProp() which automatically adds
*** properties for case sensitive conversion
loCust.address.AddProp("postalCode","96779")  && property case maintained in output

loSer = CREATEOBJECT("wwJsonSerializer")
lcJson = loSer.Serialize(loCust,.T.)
? lcJson
```

This produces:

```json
{
  "address": {
    "city": "Paia",
    "postalCode": "96779",
    "street": "10b Houdage Road"
  },
  "company": "West Wind"
}
```