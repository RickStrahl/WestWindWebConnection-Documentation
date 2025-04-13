Reference to the top level page object.

This reference is used quite a bit internally so it's quite vital. The Page reference is passed in to the Init() of the control.

Controls should check for the existance of the Page object before using it to ensure it is available. Some controls can be used outside of the page framework and if they can you want to make sure they don't have to use the Page object.