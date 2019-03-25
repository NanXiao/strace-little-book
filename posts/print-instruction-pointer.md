# Print instruction pointer

`-i` option is used to print instruction pointer of calling system call. E.g.:  

	# strace -i ls
	[00007f08c925a98b] execve("/usr/bin/ls", ["ls"], 0x7ffe81ee42c8 /* 21 vars */) = 0
	[00007f53d35a026b] brk(NULL)            = 0x55fc72a56000
	[00007f53d359f035] arch_prctl(0x3001 /* ARCH_??? */, 0x7ffc45b7e080) = -1 EINVAL (Invalid argument)
	[00007f53d35a0f3b] access("/etc/ld.so.preload", R_OK) = -1 ENOENT (No such file or directory)
	[00007f53d35a1061] openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
	[00007f53d35a0e87] fstat(3, {st_mode=S_IFREG|0644, st_size=98309, ...}) = 0
	......