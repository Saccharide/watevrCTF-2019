# Kitty

* This is an introduction level forensic challenge, where a given `jpg` file is actually more than what it seems.

* To automatically check for embedded (hidden) files and executables, I used the tool `binwalk`.

* `binwalk kitty.jpg` shows that it actually contains a `ZIP` file within itself.
* Unzipping `kitty.jpg` allows us to see a hidden text file `abc` with the flag `watevr{7h475_4c7u4lly_r34lly_cu73_7h0u6h}`


## Underlying Concept
* In order to understand how this works at a deeper level, I did some research.

* It is not black magic that programs can open certain file extensions. This is because each file extension has a corresponding header and footer. 

* For example, a `PNG` file has the header `FF D8`, and a footer `FF D9`, and a `ZIP` file has the header `50 4B`.

* Using `hexedit`, we can see the the image has a `PNG` header, and then contains a `ZIP` header at the end.

* From this we can predict that this image contains at least one hidden file. 
* Note: that this can be a false positive as some data might contain the value of a file extension header by the nature of randomness.
