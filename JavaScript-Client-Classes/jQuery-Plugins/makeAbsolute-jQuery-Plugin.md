This plug-in makes the selected elements absolutely positioned. Changes the postion: absolute setting as well as explicitly removing margins and setting the top and left properties.

<div class="syntaxbox">$.fn.makeAbsolute = function(rebase)</div>

**rebase**  
When set causes the element to be rebased under document.body to work around any issues with document containership.