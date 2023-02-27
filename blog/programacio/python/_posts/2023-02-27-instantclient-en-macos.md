---
title: Use Oracle instanclient on mac OS (Developer cannot be verified)
layout: post
---

You can install the [instantclient](https://www.oracle.com/es/database/technologies/instant-client/macos-intel-x86-downloads.html) downloading the files from Oracle.

You may have noticed that using the the installation instructions, you unzip the library on a directory but when you try to load them you will end with a *Developer cannot be verified* or something other similar message fron osx.

The problem is that the code is not signed by Oracle and osx marks it with a *com.apple.quarantine*. You can see the at sign at the end of the file anmes doing a 

```bash
> ls -l 
-r-xr-xr-x@ 1 staff       5780 Feb 22  2019 BASIC_LICENSE
 com.apple.quarantine        57 
-rw-r--r--@ 1 staff       1670 Aug 19 05:57 BASIC_README
``` 

The solution is to give exucution permissions to all files and then remove the extra @ attribute.

```bash
cd instantclient_19_3
chmod u+x *
cd ..
xattr -r -d -s com.apple.quarantine instantclient_19_3
```

Now you can load and use the library form your programs.

Taken from [Gary Gordhamer](http://oraontap.blogspot.com/2020/01/mac-os-x-catalina-and-oracle-instant.html).
