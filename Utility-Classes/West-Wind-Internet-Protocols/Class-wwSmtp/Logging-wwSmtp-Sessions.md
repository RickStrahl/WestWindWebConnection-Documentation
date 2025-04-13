If you have trouble with your SMTP sessions and you need to log requests to the mail server, you can do so both with the classic and .NET components.

**Classic Mail Mode**  
In classic mail mode you can simply specify the cLogFile property by providing a filename. When set wwSmtp will log to that file all the data that is being sent and received:

```foxpro
loSmtp.cLogFile = "c:\temp\wwsmtplog.txt"
```

**.NET Mode**  
Because .NET mode uses the the SmtpClient .NET framework component, rather than a hand built component, direct logging cannot be performed. However, .NET has extensive support for 'tracing' built into the framework and System.Net - the namespace that hosts SmtpClient - allows for detailed logging of most network protocols.

To do this you need to set up tracing in your application via a config file. Config files are tied to the host EXE file that runs - so YourApp.exe or Vfp.exe (if your running the IDE) and you need to create YourApp.Exe.config or Vfp.exe.config file to hold these config settings.

In the config file put XML like this:

```xml
<?xml version="1.0"?>
<configuration>
  <startup>   
    <!-- <supportedRuntime version="v4.0.30319"/> -->
    <!-- supportedRuntime version="v2.0.50727"/ -->    
  </startup>
  <runtime>
      <loadFromRemoteSources enabled="true"/>
  </runtime>
  <system.diagnostics>
    <trace autoflush="true" />

    <sources>
      <source name="System.Net">
        <listeners>
          <!-- <add name="MyConsole"/> -->
            <add name="MyTraceFile"/>
        </listeners>
      </source>
      <source name="System.Net.Sockets">
        <listeners>
          <!-- <add name="MyConsole"/> -->
          <add name="MyTraceFile"/>
        </listeners>
      </source>
    </sources>

    <sharedListeners>
      <add
        name="MyTraceFile"
        type="System.Diagnostics.TextWriterTraceListener"
        initializeData="c:\temp\wwsmtplog.txt" />
      <add name="MyConsole" type="System.Diagnostics.ConsoleTraceListener" />
    </sharedListeners>

    <switches>
      <add name="System.Net" value="Verbose" />
      <add name="System.Net.Sockets" value="Verbose" />
    </switches>
  </system.diagnostics>
</configuration>
```

The key here are the System.Diagnostics sections which set up trace sources and bind them to specific trace listeners. Here I'm creating a MyTraceFile listener that writes to a file which is specified in the InitializeData attribute. I also specify I want to listen for traces coming from System.Net and System.Net.Sockets. The former provides API calls made and timings while the latter provides the actual raw data that's pushed over the wire.

Note that if you are using SSL connections, the data sent won't be readable as the actual socket data is encoded. You should however be able to see the connections and packets being sent so if there are problems you should be able to see the transactions.

The information here is quite verbose, but you can probably find what you need in your SMTP conversations to determine what's failing.