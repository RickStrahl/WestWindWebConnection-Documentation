This property holds the default jQuery and jQueryUI load mode. The default value is "Default" which is synonymous with loading resources from CDN (with a local fallback) or from a fixed URL when no CDN is available.

The values this can be set to are:
<ul>
* Default - Default location usually CDN if available (for jQuery, jQuery.ui)
* CDN - Load from CDN
* WebResource - Loads from Web Connection ~/scripts folder 
</ul>

Ideally you'll leave the value at Default and always use what the internal routines prefer.