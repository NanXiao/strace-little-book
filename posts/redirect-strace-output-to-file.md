# Redirect strace's output to file
You can use `-o` option to specify a file which saves `strace`'s output:  

	# strace -o log.txt ls
	log.txt
	# more log.txt
	execve("/usr/bin/ls", ["ls"], 0x7ffe2e59b220 /* 20 vars */) = 0
	brk(NULL)                               = 0x5599582f6000
	arch_prctl(0x3001 /* ARCH_??? */, 0x7fffd38db780) = -1 EINVAL (Invalid argument)
	access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
	openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
	......

Additionally, `-A` option can be used to append log to an existing file instead of truncate it:  

	# ls -lth log.txt
	-rw-r--r-- 1 root root 3.6K Feb 28 10:07 log.txt
	# strace -o log.txt ls
	log.txt
	# ls -lth log.txt
	-rw-r--r-- 1 root root 3.6K Feb 28 10:08 log.txt
	# strace -A -o log.txt ls
	log.txt
	# ls -lth log.txt
	-rw-r--r-- 1 root root 7.2K Feb 28 10:08 log.txt
 