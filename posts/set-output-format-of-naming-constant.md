# Set output format of naming constant

By default, `strace` will decode naming constant (equals to use `-X abbrev` option):  

	# strace ls
	......
	access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
	......

If raw value is wanted, using `-X raw` option:  

	# strace -X raw ls
	......
	access("/etc/ld.so.preload", 0x4)       = -1 ENOENT (No such file or directory)
	......

`-X verbose` outputs both:  

	# strace -X verbose ls
	......
	access("/etc/ld.so.preload", 0x4 /* R_OK */) = -1 ENOENT (No such file or directory)
