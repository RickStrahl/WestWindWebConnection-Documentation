Refreshes a table's read buffers, by explicitly moving the record pointer. This causes VFP to re-read buffers from disk, to ensure data is not coming from the data refresh cache in VFP.

Use this before performing READ operations that are timing sensitive. In Web Connection for example this function is used for Session table ID lookups to ensure IDs are avaiable immediately.