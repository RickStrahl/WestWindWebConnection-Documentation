The base Url for the service. 

Optional but useful to allow switching between test/live deployments without having to hardcode URLs.

```foxpro
*** Test
cServiceUrl = "https://test-albumviewer.west-wind.com/api/"

*** Production
* cServiceUrl = "https://albumviewer.west-wind.com/api/"
```

Then in code instead of using the full URL you can use build up the URL:

```foxpro
this.CallService(this.cServiceUrl + "albums")
```