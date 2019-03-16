# Display strace's debug information

`-d` option can be used to show `strace`'s own debug information. Check following simple program:  

	# cat fork.c
	#include <sys/types.h>
	#include <unistd.h>
	
	int main(void)
	{
	        int pid = fork();
	        while (1)
	        {
	            sleep(1);
	        }
	        return 0;
	}

Build and use `strace`'s `-d` option to track it:  

	# gcc fork.c -o fork
	# strace -d ./fork
	strace: ptrace_setoptions = 0x51
	strace: PTRACE_GET_SYSCALL_INFO: Input/output error
	strace: PTRACE_GET_SYSCALL_INFO does not work
	strace: new tcb for pid 583, active tcbs:1
	strace: [wait(0x80137f) = 583] WIFSTOPPED,sig=SIGSTOP,EVENT_STOP (128)
	strace: pid 583 has TCB_STARTUP, initializing it
	strace: [wait(0x80057f) = 583] WIFSTOPPED,sig=SIGTRAP,EVENT_STOP (128)
	strace: [wait(0x00127f) = 583] WIFSTOPPED,sig=SIGCONT
	strace: [wait(0x00857f) = 583] WIFSTOPPED,sig=133
	execve("./fork", ["./fork"], 0x7ffcc9f68b48 /* 21 vars */strace: [wait(0x04057f) = 583] WIFSTOPPED,sig=SIGTRAP,EVENT_EXEC (4)
	......
	strace: [wait(0x00857f) = 583] WIFSTOPPED,sig=133
	clone(strace: [wait(0x00857f) = 583] WIFSTOPPED,sig=133
	......

The debug information is interleaved with normal output. Open another terminal to check the process relationship, and this can help knowing debug information better:  

	# pstree -p `pidof strace`
	strace(580)---fork(583)---fork(584)

Besides child processes' exit status, `strace` cares about events such as forking process, exit process, etc (please refer related [code](https://github.com/strace/strace/blob/7eeb205a348a93f256379a89dc5eb5e7e90f791e/strace.c#L1954)).