Returns a DiffGram XML string for the cursor by name. This is merely a wrapper that encapsulates the process of creating a DiffGram.

Remember that in order to support tracking of changes in order to generate a DiffGram your cursor must be in CursorSetProp("buffering",5) and SET MULTILOCKS ON.