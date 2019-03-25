# Trace child processes

Check following code:  

	# cat fork.c
	#include <sys/types.h>
	#include <unistd.h>
	
	int main(void)
	{
	        int pid = fork();
	        if (pid < 0)
	        {
	                return 1;
	        }
	        else
	        {
	                while (1)
	                {
	                        sleep(1);
	                }
	        }
	        return 0;
	}

By default, `strace` won't trace child processes spawned by `fork`, `vfork` and `clone`:  

	# gcc fork.c -o fork
	# strace ./fork
	execve("./fork", ["./fork"], 0x7ffde8bab140 /* 21 vars */) = 0
	brk(NULL)                               = 0x556c0719c000
	arch_prctl(0x3001 /* ARCH_??? */, 0x7ffd59b05130) = -1 EINVAL (Invalid argument)
	access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
	openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
	fstat(3, {st_mode=S_IFREG|0644, st_size=98317, ...}) = 0
	mmap(NULL, 98317, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f045e508000
	close(3)                                = 0
	openat(AT_FDCWD, "/usr/lib/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
	read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0000C\2\0\0\0\0\0"..., 832) = 832
	lseek(3, 792, SEEK_SET)                 = 792
	read(3, "\4\0\0\0\24\0\0\0\3\0\0\0GNU\0\201\336\t\36\251c\324\233E\371SoK\5H\334"..., 68) = 68
	fstat(3, {st_mode=S_IFREG|0755, st_size=2136840, ...}) = 0
	mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f045e506000
	lseek(3, 792, SEEK_SET)                 = 792
	read(3, "\4\0\0\0\24\0\0\0\3\0\0\0GNU\0\201\336\t\36\251c\324\233E\371SoK\5H\334"..., 68) = 68
	lseek(3, 864, SEEK_SET)                 = 864
	read(3, "\4\0\0\0\20\0\0\0\5\0\0\0GNU\0\2\0\0\300\4\0\0\0\3\0\0\0\0\0\0\0", 32) = 32
	mmap(NULL, 1848896, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f045e342000
	mprotect(0x7f045e364000, 1671168, PROT_NONE) = 0
	mmap(0x7f045e364000, 1355776, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x22000) = 0x7f045e364000
	mmap(0x7f045e4af000, 311296, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x16d000) = 0x7f045e4af000
	mmap(0x7f045e4fc000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1b9000) = 0x7f045e4fc000
	mmap(0x7f045e502000, 13888, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f045e502000
	close(3)                                = 0
	arch_prctl(ARCH_SET_FS, 0x7f045e507500) = 0
	mprotect(0x7f045e4fc000, 16384, PROT_READ) = 0
	mprotect(0x556c0647d000, 4096, PROT_READ) = 0
	mprotect(0x7f045e54a000, 4096, PROT_READ) = 0
	munmap(0x7f045e508000, 98317)           = 0
	clone(child_stack=NULL, flags=CLONE_CHILD_CLEARTID|CLONE_CHILD_SETTID|SIGCHLD, child_tidptr=0x7f045e5077d0) = 5082
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7ffd59b050d0) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7ffd59b050d0) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7ffd59b050d0) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, ^Cstrace: Process 5081 detached
	 <detached ...>
	......

To trace child processes, `-f` option need to be specified:  

	# strace -f ./fork
	execve("./fork", ["./fork"], 0x7ffcebee9288 /* 21 vars */) = 0
	brk(NULL)                               = 0x55d21e20b000
	arch_prctl(0x3001 /* ARCH_??? */, 0x7ffd52e9e610) = -1 EINVAL (Invalid argument)
	access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
	openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
	fstat(3, {st_mode=S_IFREG|0644, st_size=98317, ...}) = 0
	mmap(NULL, 98317, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7fd7e2fd8000
	close(3)                                = 0
	openat(AT_FDCWD, "/usr/lib/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
	read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0000C\2\0\0\0\0\0"..., 832) = 832
	lseek(3, 792, SEEK_SET)                 = 792
	read(3, "\4\0\0\0\24\0\0\0\3\0\0\0GNU\0\201\336\t\36\251c\324\233E\371SoK\5H\334"..., 68) = 68
	fstat(3, {st_mode=S_IFREG|0755, st_size=2136840, ...}) = 0
	mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fd7e2fd6000
	lseek(3, 792, SEEK_SET)                 = 792
	read(3, "\4\0\0\0\24\0\0\0\3\0\0\0GNU\0\201\336\t\36\251c\324\233E\371SoK\5H\334"..., 68) = 68
	lseek(3, 864, SEEK_SET)                 = 864
	read(3, "\4\0\0\0\20\0\0\0\5\0\0\0GNU\0\2\0\0\300\4\0\0\0\3\0\0\0\0\0\0\0", 32) = 32
	mmap(NULL, 1848896, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fd7e2e12000
	mprotect(0x7fd7e2e34000, 1671168, PROT_NONE) = 0
	mmap(0x7fd7e2e34000, 1355776, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x22000) = 0x7fd7e2e34000
	mmap(0x7fd7e2f7f000, 311296, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x16d000) = 0x7fd7e2f7f000
	mmap(0x7fd7e2fcc000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1b9000) = 0x7fd7e2fcc000
	mmap(0x7fd7e2fd2000, 13888, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7fd7e2fd2000
	close(3)                                = 0
	arch_prctl(ARCH_SET_FS, 0x7fd7e2fd7500) = 0
	mprotect(0x7fd7e2fcc000, 16384, PROT_READ) = 0
	mprotect(0x55d21d27e000, 4096, PROT_READ) = 0
	mprotect(0x7fd7e301a000, 4096, PROT_READ) = 0
	munmap(0x7fd7e2fd8000, 98317)           = 0
	clone(child_stack=NULL, flags=CLONE_CHILD_CLEARTID|CLONE_CHILD_SETTID|SIGCHLD, child_tidptr=0x7fd7e2fd77d0) = 5087
	strace: Process 5087 attached
	[pid  5086] nanosleep({tv_sec=1, tv_nsec=0},  <unfinished ...>
	[pid  5087] nanosleep({tv_sec=1, tv_nsec=0},  <unfinished ...>
	[pid  5086] <... nanosleep resumed> 0x7ffd52e9e5b0) = 0
	[pid  5087] <... nanosleep resumed> 0x7ffd52e9e5b0) = 0
	[pid  5087] nanosleep({tv_sec=1, tv_nsec=0},  <unfinished ...>
	[pid  5086] nanosleep({tv_sec=1, tv_nsec=0},  <unfinished ...>
	......

Let's see a more complicated case:  

	#include <sys/types.h>
	#include <unistd.h>
	#include <omp.h>
	
	int main(void)
	{
	    #pragma omp parallel num_threads(2)
	    {
	        sleep(30);
	        int pid = fork();
	        while (1)
	        {
	            sleep(1);
	        }
	    }
		return 0;
	}

Above code will spawn `2` threads, and every thread will fork another child process after waiting `30` seconds:  

	# ./fork &
	[1] 5239
	# ps -T 5239
	  PID  SPID TTY      STAT   TIME COMMAND
	 5239  5239 pts/1    Sl     0:00 ./fork
	 5239  5240 pts/1    Sl     0:00 ./fork

Use any `SPID` (`5239` or `5240` in this case), `strace` will trace all child processes of threads belong to current process:  

	# strace -p 5239 -f
	strace: Process 5239 attached with 2 threads
	[pid  5240] restart_syscall(<... resuming interrupted nanosleep ...> <unfinished ...>
	[pid  5239] restart_syscall(<... resuming interrupted nanosleep ...> <unfinished ...>
	[pid  5240] <... restart_syscall resumed> ) = 0
	[pid  5239] <... restart_syscall resumed> ) = 0
	[pid  5239] clone(child_stack=NULL, flags=CLONE_CHILD_CLEARTID|CLONE_CHILD_SETTID|SIGCHLD, child_tidptr=0x7f6dd132ced0) = 5245
	[pid  5240] futex(0x7f6dd14f47a0, FUTEX_WAIT_PRIVATE, 2, NULL) = -1 EAGAIN (Resource temporarily unavailable)
	[pid  5240] clone(strace: Process 5245 attached
	child_stack=NULL, flags=CLONE_CHILD_CLEARTID|CLONE_CHILD_SETTID|SIGCHLD, child_tidptr=0x7f6dd132b9d0) = 5246
	[pid  5240] futex(0x7f6dd14f47a0, FUTEX_WAKE_PRIVATE, 1) = 0
	[pid  5240] nanosleep({tv_sec=1, tv_nsec=0},  <unfinished ...>
	[pid  5239] futex(0x7f6dd14f47a0, FUTEX_WAKE_PRIVATE, 1) = 0
	[pid  5239] nanosleep({tv_sec=1, tv_nsec=0}, strace: Process 5246 attached
	 <unfinished ...>
	[pid  5246] set_robust_list(0x7f6dd132b9e0, 24) = 0
	[pid  5246] futex(0x7f6dd14f47a0, FUTEX_WAKE_PRIVATE, 1) = 0
	[pid  5246] nanosleep({tv_sec=1, tv_nsec=0},  <unfinished ...>
	[pid  5245] set_robust_list(0x7f6dd132cee0, 24) = 0
	[pid  5245] nanosleep({tv_sec=1, tv_nsec=0},  <unfinished ...>
	......
