Returns the Windows OS temp file path.

Unlike SYS(2023) this method returns the Windows TEMP path for the current user rather than VFP's temporary file path even though these may point at the same place.