# Show statistics of system calls  

`-c` option can be used to summarize the statistics of every system call: 

	# strace -c ls
	project
	% time     seconds  usecs/call     calls    errors syscall
	------ ----------- ----------- --------- --------- ----------------
	 20.92    0.000050          50         1           munmap
	 19.25    0.000046          23         2           getdents64
	 14.64    0.000035           5         6           close
	 10.46    0.000025          25         1           write
	 10.46    0.000025          12         2           ioctl
	  9.21    0.000022           4         5           fstat
	  9.21    0.000022           7         3           brk
	  5.86    0.000014           3         4           openat
	  0.00    0.000000           0         5           read
	  0.00    0.000000           0         3           lseek
	  0.00    0.000000           0        12           mmap
	  0.00    0.000000           0         5           mprotect
	  0.00    0.000000           0         1         1 access
	  0.00    0.000000           0         1           execve
	  0.00    0.000000           0         2         1 arch_prctl
	------ ----------- ----------- --------- --------- ----------------
	100.00    0.000239                    53         2 total

`strace` shows the time and error count of every system call. Similarly, `-C` option can print both regular output and statistics:  

	# strace -C ls
	execve("/usr/bin/ls", ["ls"], 0x7ffe91857fa8 /* 21 vars */) = 0
	brk(NULL)                               = 0x555bd69bc000
	arch_prctl(0x3001 /* ARCH_??? */, 0x7ffc6ea19940) = -1 EINVAL (Invalid argument)
	access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
	openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
	fstat(3, {st_mode=S_IFREG|0644, st_size=98317, ...}) = 0
	mmap(NULL, 98317, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7fdf81c6f000
	
	......
	close(1)                                = 0
	close(2)                                = 0
	exit_group(0)                           = ?
	+++ exited with 0 +++
	% time     seconds  usecs/call     calls    errors syscall
	------ ----------- ----------- --------- --------- ----------------
	 26.89    0.000256          21        12           mmap
	 13.97    0.000133          33         4           openat
	 11.45    0.000109          21         5           fstat
	  9.56    0.000091          15         6           close
	  7.98    0.000076          15         5           mprotect
	......

Notice that `-c` and `-C` options are mutually exclusive. Please notice the time here measured is system time of every system call. If wall time is required, it is `-w` option :  

	# strace -w -c ls
	project
	% time     seconds  usecs/call     calls    errors syscall
	------ ----------- ----------- --------- --------- ----------------
	 19.85    0.000297          24        12           mmap
	 17.98    0.000270         269         1           execve
	  9.32    0.000140          23         6           close
	......  

There is another `-S sortby` option to customize histogram output sorted by `calls`, `name` and `nothing` (default is sorted by `time`). E.g.:  

	# strace -c -S calls ls
	project
	% time     seconds  usecs/call     calls    errors syscall
	------ ----------- ----------- --------- --------- ----------------
	 27.66    0.000164          13        12           mmap
	  8.26    0.000049           8         6           close
	  7.76    0.000046           9         5           read
	  5.40    0.000032           6         5           fstat
	 13.15    0.000078          15         5           mprotect
	  5.23    0.000031           7         4           openat
	  5.23    0.000031          10         3           lseek
	  4.05    0.000024           8         3           brk
	  4.05    0.000024          12         2           ioctl
	  1.69    0.000010           5         2         1 arch_prctl
	  7.93    0.000047          23         2           getdents64
	  4.55    0.000027          27         1           write
	  5.06    0.000030          30         1           munmap
	  0.00    0.000000           0         1         1 access
	  0.00    0.000000           0         1           execve
	------ ----------- ----------- --------- --------- ----------------
	100.00    0.000593                    53         2 total
