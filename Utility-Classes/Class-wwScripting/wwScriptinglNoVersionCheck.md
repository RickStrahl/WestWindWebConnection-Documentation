If .T. doesn't check or attempt to compile the file, but just executes the FXP. Assumes the FXP exists and fails if it does not exist.

This setting provides fastest performance but requires explicit precompilation.

If not set, pages are checked by comparing the script file and the FXP file dates. If the script file date is newer the page is recompiled before execution.