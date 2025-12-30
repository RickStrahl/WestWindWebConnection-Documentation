Determines how messaging between the Web Server and FoxPro works in Web Connection:

* File
* COM


File mode uses messaging semaphore file polling to check for new requests. FoxPro picks up the message files, processes the request and returns a response message file. File servers run as standalone EXEs.

COM mode uses COM based instances to process requests via RPC method calls that use direct single method, transactional COM method calls. COM Servers run as registered DCOM (.exe) components.

Both mechanisms work mostly interchangeably and you can switch between them.