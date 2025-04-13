Runs an external process using the `CreateProcessEx` API **including the ability to redirect output to a file** and Wait for completion.

`CreateProcessEx` has a number of advantages over using the RUN command including a much longer command line limit and the ability to understand long filenames correctly.

`CreateProcessEx` provides the ability to pipe output into an output file - a feature that is not available for the simpler [CreateProcess](VFPS://Topic/_1BF02HPQN) function.