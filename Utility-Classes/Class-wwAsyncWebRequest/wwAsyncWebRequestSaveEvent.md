This method saves the current event stored in the oEvent  member. This method is called by all other methods that write and updated events to disk.

Typically you won't call this method directly, but in basic form you can use LoadEvent() and SaveEvent() to manually manage all event processing directly.