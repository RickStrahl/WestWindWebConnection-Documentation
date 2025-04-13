Retrieves a pending event from the event queue and sets the oEvent member with the retrieved event object.

Updates the STARTED property of the the event object. The record is locked to ensure only one client app at a time can retrieve an event through this mechanism.