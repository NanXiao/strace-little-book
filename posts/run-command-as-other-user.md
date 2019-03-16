# Run command as other user  

`-u username` option can run command as other user, and this option only take effect when executing as `root`. Check following example:  

	# cat getuid.c
	#include <unistd.h>
	#include <sys/types.h>
	#include <stdio.h>
	
	int main(void)
	{
	    printf("uid is %d\n", getuid());
	    return 0;
	}

Build and run it as `root`:  

	# gcc getuid.c -o getuid
	# strace ./getuid
	execve("./getuid", ["./getuid"], 0x7ffce778f8a0 /* 12 vars */) = 0
	brk(NULL)                               = 0x563fe210a000
	......
	write(1, "uid is 0\n", 9uid is 0
	)               = 9
	exit_group(0)                           = ?
	+++ exited with 0 +++

It shows `uid` is `0`. Run the program as another user:  

	# strace -u nan ./getuid
	......
	write(1, "uid is 1000\n", 12uid is 1000
	)           = 12
	exit_group(0)                           = ?
	+++ exited with 0 +++

This time, `uid` is `1000`.
	
