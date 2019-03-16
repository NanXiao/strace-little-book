# Attach to running processes

`strace` can attach and trace running processes. Check following simple example:  

	# cat dead_loop.c
	#include <unistd.h>
	
	int main(void)
	{
	        while (1)
	        {
	                sleep(1);
	        }
	        return 0;
	}

Build and launch two processes:  

	# gcc dead_loop.c -o dead_loop
	# ./dead_loop &
	[1] 2194
	# ./dead_loop &
	[2] 2195

Use `-p` option to trace one process:  

	# strace -p 2194
	strace: Process 2194 attached
	restart_syscall(<... resuming interrupted nanosleep ...>) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7ffc037bf730) = 0
	......

Use `-p` option to trace multiple processes:  

	# strace -p 2194,2195
	strace: Process 2194 attached
	strace: Process 2195 attached
	[pid  2194] restart_syscall(<... resuming interrupted nanosleep ...> <unfinished ...>
	[pid  2195] restart_syscall(<... resuming interrupted nanosleep ...> <unfinished ...>
	[pid  2194] <... restart_syscall resumed> ) = 0
	[pid  2194] nanosleep({tv_sec=1, tv_nsec=0},  <unfinished ...>
	[pid  2195] <... restart_syscall resumed> ) = 0
	......

`strace` can also utilize `pidof` command to trace processes:  

	# strace -p "`pidof dead_loop`"
	strace: Process 2195 attached
	strace: Process 2194 attached
	[pid  2194] restart_syscall(<... resuming interrupted nanosleep ...> <unfinished ...>
	[pid  2195] restart_syscall(<... resuming interrupted nanosleep ...>) = 0
	[pid  2195] nanosleep({tv_sec=1, tv_nsec=0},  <unfinished ...>
	[pid  2194] <... restart_syscall resumed> ) = 0
	......

Or use `pgrep`:  

	# strace -p "`pgrep dead_loop`"
	strace: Process 2194 attached
	strace: Process 2195 attached
	[pid  2195] restart_syscall(<... resuming interrupted nanosleep ...> <unfinished ...>
	[pid  2194] restart_syscall(<... resuming interrupted nanosleep ...>) = 0
	[pid  2194] nanosleep({tv_sec=1, tv_nsec=0},  <unfinished ...>
	[pid  2195] <... restart_syscall resumed> ) = 0
	[pid  2195] nanosleep({tv_sec=1, tv_nsec=0},  <unfinished ...>
	......

