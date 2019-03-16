# Print stack trace of every system call

`-k` option is used to print stack trace of every system call. To enable this option, `ENABLE_STACKTRACE` is needed to build `strace` (code is [here](https://github.com/strace/strace/blob/e0f0071b36215de8a592bf41ec007a794b550d45/strace.c#L50)). E.g.:  

	# strace -k ls
	execve("/usr/bin/ls", ["ls"], 0x7ffe9c8243b8 /* 21 vars */) = 0
	 > /usr/lib/libc-2.28.so(execve+0xb) [0xc898b]
	 > /usr/bin/strace(+0x0) [0xa25a4]
	 > /usr/bin/strace(+0x0) [0xa40ad]
	 > /usr/bin/strace(+0x0) [0x6f24c]
	 > /usr/lib/libc-2.28.so(__libc_start_main+0xf3) [0x24223]
	 > /usr/bin/strace(+0x0) [0x6fa7e]
	brk(NULL)                               = 0x55d840fe5000
	 > /usr/lib/ld-2.28.so(__brk+0xb) [0x1b26b]
	 > /usr/lib/ld-2.28.so(_dl_sysdep_start+0x2f1) [0x19ef1]
	 > /usr/lib/ld-2.28.so(_dl_start+0x288) [0x3088]
	 > /usr/lib/ld-2.28.so() [0x2008]
	......
