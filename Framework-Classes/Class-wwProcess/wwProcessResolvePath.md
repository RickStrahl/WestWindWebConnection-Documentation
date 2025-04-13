Resolves `~/` in a URL like `~/page.wc` or `~/subfolder/page.wc` to a site relative path like `/wconnect/page.wc` or `/wconnect/subfolder.wc`. 

Using `~/` allows for running a site in a virtual directory (ie. base is `/wconnect`) **or** in a Root site (ie. base is `/`) and avoids having to hardcode the site relative path. `~/` figures out the site's base path and adjusts. 

It's recommended you always use `~/` paths for any resources for cleaner handling of relative links and resources.

More info this blog post:

[Root Relative Paths in Web Connection](https://www.west-wind.com/wconnect/weblog/ShowEntry.blog?id=807&id=807)