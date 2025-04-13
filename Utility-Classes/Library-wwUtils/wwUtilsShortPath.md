Converts a Long Windows filename into a short 8.3 compliant path/filename. Use API so this is a valid 8.3 safe file path as recorded by the Operating System.

> #### @icon-warning Non-existing Files return Empty String
> If the file passed in does not exist this function returns an empty string. The underlying Windows API function requires that the file exists, this wrapper also returns empty string to indicate that it didn't work.
>
> Either ensure that the file exists before calling this method, or explicitly check for an empty result and use the original filename or some other alternative.