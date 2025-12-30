Determines how many server instances of the server are loaded both in COM mode (automatically) or in File Mode (via explicit launch or AutoStart option).

Multiple server instances are needed to process simultaneous FoxPro requests. More instances provide more parallelism.


Default is 2 and for production use we recommend a minimum of two servers.