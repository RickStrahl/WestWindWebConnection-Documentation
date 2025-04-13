The optional name of the Cache key used if caching the output from the control.

Optional - use only if you need to share the content with multiple controls on multiple forms otherwise the default will be the UniqueKey of the control.

By default Web Connection will generate a unique cache key based on the URL and UniqueId of the control. 

Use the this property only if you need to share a cached control in multiple places of your application. In that scenario all of the controls will share the same cache instance.