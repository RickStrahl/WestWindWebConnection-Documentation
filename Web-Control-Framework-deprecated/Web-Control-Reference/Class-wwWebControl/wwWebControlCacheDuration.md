If this value is > 0 the output of this control is cacheable. Value is in seconds.

Cached content is stored using the wwCache object in Server.oCache. 

Be very careful with Cached content. Once cached you can not easily uncache controls because the cache is read very early in the page cycle. The only way to clear items is to effectively purge the item manually (for example:  Server.oCache.RemoveItem(this.MyControl.CacheKey)).

Note that caching will capture whatever value the control has at render time. This can have some interesting side effects for some controls. For example, a listbox the content will be the list's items as well as any possible selections, so any selections or non-selections will always be restored on the next hit. Caching works best for content controls that like labels, containers, or other complex objects that might render repeated values that stay the same for the duration specified.

Currently the Cache implementation is Render time specific only, so all code up to Render() executes. Only when Render fires is the cache checked. This behavior may change in the future.