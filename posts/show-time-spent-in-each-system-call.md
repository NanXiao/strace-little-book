# Show time spent in each system call

`-T` option is used to print time spent in each system call. E.g.:  

	# strace -T ls
	execve("/usr/bin/ls", ["ls"], 0x7ffd141a4ee8 /* 20 vars */) = 0 <0.000354>
	brk(NULL)                               = 0x55b222b07000 <0.000080>
	arch_prctl(0x3001 /* ARCH_??? */, 0x7ffd1758c950) = -1 EINVAL (Invalid argument) <0.000051>
	access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory) <0.000083>
	......