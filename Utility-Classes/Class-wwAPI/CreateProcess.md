Runs an external process using the CreateProcess API. CreateProcess has a number of advantages over using the RUN command including a much longer command line limit and the ability to understand long filenames correctly.

This function is a standalone function and not a member of the wwAPI class.

> Note this method **does not support re-routing StdOut to a file**. For that purpose use [CreateProcessEx](vfps://Topic/_1H30VQ01M).