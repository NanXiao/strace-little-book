# Align return value column

By default, the alignment column for results is `40`:  

	# strace ls
	execve("/usr/bin/ls", ["ls"], 0x7ffca1e1f070 /* 21 vars */) = 0
	brk(NULL)                               = 0x55c6d34e2000
	arch_prctl(0x3001 /* ARCH_??? */, 0x7ffd28e4cd10) = -1 EINVAL (Invalid argument)
	access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
	openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
	fstat(3, {st_mode=S_IFREG|0644, st_size=98317, ...}) = 0
	mmap(NULL, 98317, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f6ebc2ae000
	close(3)                                = 0
	openat(AT_FDCWD, "/usr/lib/libcap.so.2", O_RDONLY|O_CLOEXEC) = 3
	......

`-a` option can be used to adjust the alignment:  

	# strace -a 80 ls
	execve("/usr/bin/ls", ["ls"], 0x7fffe93ef0d0 /* 21 vars */)                     = 0
	brk(NULL)                                                                       = 0x562a6bfa1000
	arch_prctl(0x3001 /* ARCH_??? */, 0x7ffd332954c0)                               = -1 EINVAL (Invalid argument)
	access("/etc/ld.so.preload", R_OK)                                              = -1 ENOENT (No such file or directory)
	openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC)                        = 3
	fstat(3, {st_mode=S_IFREG|0644, st_size=98317, ...})                            = 0
	mmap(NULL, 98317, PROT_READ, MAP_PRIVATE, 3, 0)                                 = 0x7f2f8fd22000
	close(3)                                                                        = 0
	......

Tune this parameter according to your screen or your habit.

