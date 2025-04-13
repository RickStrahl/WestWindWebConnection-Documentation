The oEvent record contains the actual event data. 

This object is loaded by the various operations that submit, access and edit the events. The data is loaded from a table that contains the following fields:

<pre>      ID          C (13),;    && Event Id
      TITLE       C (60),;		&& Title
      RETURNDATA  M ,;		&& Any return data the server can set
      INPUTDATA   M ,;		&& Any input data the client wants to send
      TYPE        C (50),;		&& Content type of the result data (optoinal)
      PRIORITY N(1),;			&& Priority of request respected by GetNextEvent - higher value processes first
      SUBMITTED   T ,;		&& Time event was submitted
      COMPLETED   T ,;		&& Time event was completed
      Expire      I,;				&& Event timeout from the Submitted time
      STATUS      M ,;			&& Current Status that server can write to let client now of progress
      USERNAME    C(20),;		&& Optional username field to know who submitted the event
      ChkCounter I,;			&& Counter updated everytime CheckForCompletion is called
      Cancelled  L,;			&& Cancelled flag for this event
      PROPERTIES  M )		&& Freeform XML Properties field used by SetProperty/GetProperty
</pre>