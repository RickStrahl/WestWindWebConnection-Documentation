When running long requests that could potentially hold up your server pool you can use a special COM operation mode which allows you to instantiate a server once, run the request and unload the server after completion of the task. This allows for long running operations like reports to process independently of the Web Connection Server pool. 

Some applications may be things like credit card processing, communicating with a terminal program, running a complex query, or communicating with another server over HTTP.

### How it works
In order to use this feature your application must be able to function in COM messaging mode. You can either use an existing COM object to handle these requests or set up a special server altogether to handle the request and potentially run this server even on a remote machine.

This feature is implemented as an querystring parameter on the url. For example, to hit the following URL:

**http://localhost/wconnect/slowpage.wwd?name=Rick&company=west+wind**  

or on a script map page:

**http://localhost/wconnect/slowpage.wwd?name=Rick&company=west+wind&instancing=single**  

or without any query string parameters:

**http://localhost/wconnect/slowpage.wwd?instancing=single**  

When you run the latter links you'll see an additional instance of your Web Connection server pop up, process the request and then release and disappear.

> #### @icon-info-circle COM Only Feature
> This feature is available only when running COM messaging. If running filebased the instancing querystring variable is simply ignored and the request is processed as normal.