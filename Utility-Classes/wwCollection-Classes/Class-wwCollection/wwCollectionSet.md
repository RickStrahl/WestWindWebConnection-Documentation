Assigns a value to the collection by adding if it doesn't exist, or doing nothing if it already exists.

Use this function primarily with `RequiresUniqueItems=.T.` collections.

Behavior is identical to `.Add()` with `RequiresUniqueItems=.T.`, but this method **always** checks for existing items to replace if they exist first.