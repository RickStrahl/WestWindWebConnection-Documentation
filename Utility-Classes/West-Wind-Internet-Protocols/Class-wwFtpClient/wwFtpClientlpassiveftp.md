Flag that determines whether to use passive or active FTP

You should always prefer passive use as it uses a single port (21 typically) for both send and receiving. Using active connections requires multiple connections that are dynamically negotiated and require special firewall authorization or prompts from the firewall to approve connections.