Creates a zip file from a list of files.

Files can be added either as fully qualified files or file wildcards that can glom many files at once. Files or wildcards are separated by commas.

If you want to create a folder structure inside of the zip file - ie. capture the recursive file structure of the files imported - you can specify a base folder that acts as a the 'root' for the folder structure inside of the zip file. If you specify a base folder it becomes the root and all files added are added as relative files to this root in a zip folder structure. If no base path is provided files are added in flat format.