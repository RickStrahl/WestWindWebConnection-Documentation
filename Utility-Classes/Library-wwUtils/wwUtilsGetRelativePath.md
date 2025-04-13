Returns a relative path (`..\file.txt` or `SubFolder\file.txt`) from a full path relative to a given base path.

Similar to `Sys(2014)` (Minimum Path), but this function preserves path case *if the file exists* - otherwise lower case is returned.