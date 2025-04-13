Code that is translated into the *onclick* client event.

**Note:**  
If you use OnClientClick() on a submit button the button will still submit the form unless you return false from your JavaScript handler. 

Note that when UseSubmitBehavior is false you cannot fire 'click' events for this button since the button officially is not being submitted unless you manually hook up a PostBackEventReference.