# Show timestamp of system call

`-t` option shows the wall clock time of every system call:  

	# strace -t ls
	13:20:19 execve("/usr/bin/ls", ["ls"], 0x7fff489a7c98 /* 21 vars */) = 0
	13:20:19 brk(NULL)                      = 0x55cd60264000     
	......

`-tt` option will append microseconds:  

	# strace -tt ls
	13:21:07.553038 execve("/usr/bin/ls", ["ls"], 0x7ffc5238f338 /* 21 vars */) = 0
	13:21:07.553713 brk(NULL)               = 0x559753feb000
	......

`-ttt` option prints seconds since epoch time:  

	# strace -ttt ls
	1552540937.975302 execve("/usr/bin/ls", ["ls"], 0x7ffdef5c3918 /* 21 vars */) = 0
	1552540937.976413 brk(NULL)             = 0x55fe713d8000
	......

`-r` option prints a relative timestamp upon entry to each system call.  This records the time difference between the beginning of successive system calls:  

	# strace -r ls
    0.000000 execve("/usr/bin/ls", ["ls"], 0x7ffc3ad8a5e8 /* 21 vars */) = 0
    0.000623 brk(NULL)                 = 0x55cce063d000
    0.000172 arch_prctl(0x3001 /* ARCH_??? */, 0x7ffd8e8867d0) = -1 EINVAL (Invalid argument)
	......