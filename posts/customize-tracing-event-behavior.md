# Customize tracing event behavior

`-e expression` is the most complicated option which you use a qualifying expression to denote which events to trace or how to trace them. The format of expression is:  

	[qualifier=][!][?]value1[,[?]value2]...

Qualifier can be `trace` (or `t`), `abbrev`(or `a`), `verbose` (or `v`), `raw` (or `x`), `signal`/`signals` (or `s`), `read`/`reads` (or `r`), `write`/`writes` (or `w`), `fault`, `inject`, or `kvm`. If no qualifier is specified, `trace` is the used. E.g., `-e open` is equal to `-e trace=open` or `-e t=open`.  

The value part can contain one or more values, and it depends on the qualifier. E.g., `-e t=open,write` means only output `open` and `write` system calls. `!` is used to filter out the specified system call. E.g.:  

	# strace -e trace=\!write ls
This won't trace `write` system call (The `!` need to be escaped in `bash` shell). `?` is used to suppress the error. Compare the following outputs:

	# strace -e trace=op ls
	strace: invalid system call 'op'
	# strace -e trace=?op ls
	project
	+++ exited with 0 +++

"`@64`", "`@32`", or "`@x32`" suffixes can be used to specify system calls only for the `64-bit`, `32-bit`, or `32-on-64-bit` respectively.    

The value can also be `all` or `none` which mean following all system calls or nothing:  

	# strace -e trace=all ls
	execve("/usr/bin/ls", ["ls"], 0x7ffd5ae4ff10 /* 20 vars */) = 0
	brk(NULL)                               = 0x55aae08a1000
	arch_prctl(0x3001 /* ARCH_??? */, 0x7ffcdecdc3e0) = -1 EINVAL (Invalid argument)
	access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
	openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
	fstat(3, {st_mode=S_IFREG|0644, st_size=98317, ...}) = 0
	......
	# strace -e trace=none ls
	project
	+++ exited with 0 +++

## Values for trace qualifier  
Below table describes the valid values for `trace` qualifier (Most are just copied from [strace manual](http://man7.org/linux/man-pages/man1/strace.1.html)):  

<table>
  <tr>
    <th>Value</th>
    <th>Meaning</th> 
  </tr>
  <tr>
    <td>set</td>
    <td>Trace the system calls in set. E.g., "strace -e write ls".</td> 
  </tr>
  <tr>
    <td>/regex</td>
    <td>Trace all the system calls which match regular expression. E.g., "strace -e /wr* ls".</td> 
  </tr>
  <tr>
    <td>%file</td>
    <td>Trace all the system calls which take a file name as an argument. It incules open, stat, etc.</td> 
  </tr>
  <tr>
    <td>%process</td>
    <td>Trace all the system calls which involve process management.Such as fork, wait, etc.</td> 
  </tr>
  <tr>
    <td>%network(or %net)</td>
    <td>Trace all the network related system calls.</td> 
  </tr>
  <tr>
    <td>%signal</td>
    <td>Trace all signal related system calls.</td> 
  </tr>
  <tr>
    <td>%ipc</td>
    <td>Trace all IPC related system calls.</td> 
  </tr>
  <tr>
    <td>%desc</td>
    <td>Trace all file descriptor related system calls.</td> 
  </tr>
  <tr>
    <td>%memory</td>
    <td>Trace all memory mapping related system calls.</td> 
  </tr>
  <tr>
    <td>%stat</td>
    <td>Trace stat syscall variants.</td> 
  </tr>
  <tr>
    <td>%lstat</td>
    <td>Trace lstat syscall variants.</td> 
  </tr>
  <tr>
    <td>%fstat</td>
    <td>Trace fstat and fstatat syscall variants.</td> 
  </tr>
  <tr>
    <td>%%stat</td>
    <td>Trace syscalls used for requesting file status (stat, lstat, fstat, fstatat, statx, and their variants).</td> 
  </tr>
  <tr>
    <td>%statfs</td>
    <td>Trace statfs, statfs64, statvfs, osf_statfs, and osf_statfs64 system calls. The same effect can be achieved with -e trace=/^(.*_)?statv?fs regular expression.</td> 
  </tr>
  <tr>
    <td>%fstatfs</td>
    <td>Trace fstatfs, fstatfs64, fstatvfs, osf_fstatfs, and osf_fstatfs64 system calls.  The same effect can be achieved with -e trace=/fstatv?fs regular expression.</td> 
  </tr>
  <tr>
    <td>%%statfs</td>
    <td>Trace syscalls related to file system statistics (statfs-like, fstatfs-like, and ustat). The same effect can be achieved with -e trace=/statv?fs|fsstat|ustat regular expression.</td> 
  </tr>
  <tr>
    <td>%pure</td>
    <td>Trace syscalls that always succeed and have no arguments. Currently, this list includes arc_gettls, getdtablesize, getegid, getegid32, geteuid, geteuid32, getgid, getgid32, getpagesize, getpgrp, getpid, getppid, get_thread_area (on architectures other than x86), gettid, get_tls, getuid, getuid32, getxgid, getxpid, getxuid, kern_features, and metag_get_tls.</td> 
  </tr>
</table>

To know detailed information about which category a specific system call belongs to, you can refer [sysent.h](https://github.com/strace/strace/blob/master/sysent.h), [sysent_shorthand_defs.h](https://github.com/strace/strace/blob/master/sysent_shorthand_defs.h), and `syscallent.h` for your machine (E.g., `X86_64` file is [here](https://github.com/strace/strace/blob/master/linux/x86_64/syscallent.h)).  

## abbrev qualifier

`abbrev` qualifier is used to omit printing each member of large structures. The default value is `all`. The following example shows how to check environment variables when executing `execve` system call:  

	# strace -e trace=execve -e abbrev=none ls
	execve("/usr/bin/ls", ["ls"], ["SHELL=/bin/bash", "PWD=/root", "LOGNAME=root", "XDG_SESSION_TYPE=tty", "HOME=/root", "LANG=C", "SSH_CONNECTION=10.218.195.134 65"..., "XDG_SESSION_CLASS=user", "TERM=xterm", "USER=root", "SHLVL=1", "XDG_SESSION_ID=19", "XDG_RUNTIME_DIR=/run/user/0", "SSH_CLIENT=10.218.195.134 65285 "..., "PATH=/root/.cargo/bin:/usr/local"..., "DBUS_SESSION_BUS_ADDRESS=unix:pa"..., "HG=/usr/bin/hg", "MAIL=/var/spool/mail/root", "SSH_TTY=/dev/pts/0", "_=/usr/bin/strace"]) = 0
	+++ exited with 0 +++
	
Actually, `strace` has `-v` option which just leverages `abbrev` qualifier (code is [here](https://github.com/strace/strace/blob/e0f0071b36215de8a592bf41ec007a794b550d45/strace.c#L1690)):  
		
		......
		case 'v':
			qualify("abbrev=none");
			break;
		......
	
## verbose qualifier
`verbose` qualifier is used to instruct whether deference structures of system calls or not. The default value is `all`. E.g., if you don't want to deference structures of `execve`, you can do this:  

	# strace -e trace=execve -e verbose=execve ls
	execve("/usr/bin/ls", ["ls"], 0x7fffc8641bd0 /* 20 vars */) = 0
	+++ exited with 0 +++

## raw qualifier

`raw` qualifier is used to print undecoded arguments in hexadecimal format. You can compare the output of `access` without and with `raw` qualifier:  

	# strace -e access  ls
	access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
	+++ exited with 0 +++
	# strace -e access -e raw=access ls
	access(0x7f11091183a0, 0x4)             = -1 ENOENT (No such file or directory)
	+++ exited with 0 +++

## signal qualifier
By default, `strace` will capture all signals. `signal` qualifier can be used to customize which signals should be traced. Check the following code:  

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

Build and run it in one terminal:  

	# gcc dead_loop.c -o dead_loop
	# ./dead_loop

Use `strace` to track it in another terminal:  

	# strace -p `pidof dead_loop`
	strace: Process 18085 attached
	restart_syscall(<... resuming interrupted nanosleep ...>) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7ffd8492c6f0) = 0
	......

Press `Ctrl+C` in first terminal to kill `dead_loop` process, you will find `SIGINT` related information is printed out in second terminal:  

	# strace -p `pidof dead_loop`
	strace: Process 18085 attached
	restart_syscall(<... resuming interrupted nanosleep ...>) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7ffd8492c6f0) = 0
	......
	nanosleep({tv_sec=1, tv_nsec=0}, {tv_sec=0, tv_nsec=825212664}) = ? ERESTART_RESTARTBLOCK (Interrupted by signal)
	--- SIGINT {si_signo=SIGINT, si_code=SI_KERNEL} ---
	+++ killed by SIGINT +++
 
If you ignore `SIGINT`, the `SIGINT` won't be showed:  

	# strace -p `pidof dead_loop` -e signal=\!int
	strace: Process 18091 attached
	restart_syscall(<... resuming interrupted nanosleep ...>) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7ffc92b62710) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7ffc92b62710) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7ffc92b62710) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, {tv_sec=0, tv_nsec=361293365}) = ? ERESTART_RESTARTBLOCK (Interrupted by signal)

## read and write qualifiers  

`read`/`write` qualifiers are used to perform a full hexadecimal and ASCII dump of all the data read/write from file descriptors. Check following example:  

	# strace -e read=3 ls
	execve("/usr/bin/ls", ["ls"], 0x7ffd0fd7d750 /* 21 vars */) = 0
	......
	read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0  \0\0\0\0\0\0"..., 832) = 832
	 | 00000  7f 45 4c 46 02 01 01 00  00 00 00 00 00 00 00 00  .ELF............ |
	 | 00010  03 00 3e 00 01 00 00 00  20 20 00 00 00 00 00 00  ..>.....  ...... |
	 | 00020  40 00 00 00 00 00 00 00  38 52 00 00 00 00 00 00  @.......8R...... |
	 | 00030  00 00 00 00 40 00 38 00  09 00 40 00 16 00 15 00  ....@.8...@..... |
	 | 00040  01 00 00 00 04 00 00 00  00 00 00 00 00 00 00 00  ................ |
	 | 00050  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  ................ |
	 | 00060  b0 14 00 00 00 00 00 00  b0 14 00 00 00 00 00 00  ................ |
	......
	
## inject qualifier

The definition of `inject` qualifier is like this:  

	-e inject=set[:error=errno|:retval=value][:signal=sig][:syscall=syscall][:delay_enter=usecs][:delay_exit=usecs][:when=expr]
		
It is used to tamper a specific set of system calls.  

`:error=errno` can set the wrong return value of a system call. E.g.:  

	# strace -e inject=read:error=1 ls
	......
	read(3, 0x7ffca47eb418, 832)            = -1 EPERM (Operation not permitted) (INJECTED)
	close(3)                                = 0
	......

`:retval=value` does the reverse thing: set the correct return value:  

	# strace -e inject=arch_prctl:retval=0 ls
	......                              = 0x561afd1f6000
	arch_prctl(0x3001 /* ARCH_??? */, 0x7ffdcba37700) = 0 (INJECTED)

Please notice `:error=errno` and `:retval=value` are mutually exclusive.  

`:signal=sig` sets which signal will be delivered when entering a system call:  
	
	# strace -e inject=read:signal=SIGSEGV ls
	......
	read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0  \0\0\0\0\0\0"..., 832) = 832
	--- SIGSEGV {si_signo=SIGSEGV, si_code=SI_KERNEL, si_addr=NULL} ---
	+++ killed by SIGSEGV (core dumped) +++
	Segmentation fault (core dumped)

`:delay_enter=usecs`/`:delay_exit=usecs` are used to set how many microseconds are delayed before entering/exiting system calls:  

	# strace -e inject=read:delay_enter=10:delay_exit=10 ls

`:syscall=syscall` is used to inject the specified system call, only "pure" system call is allowed now (please refer `Values for trace qualifier` section).  

`:when=expr` is used to control the frequency of injection. By default every invocation will be injected. The format of the expression is one of the following:

<table>
  <tr>
    <th>Frequency</th>
    <th>Meaning</th> 
  </tr>
  <tr>
    <td>first</td>
    <td>For every syscall from the set, perform an injection for the syscall invocation number first only.</td> 
  </tr>
  <tr>
    <td>first+</td>
    <td>For every syscall from the set, perform injections for the syscall invocation number first and all subsequent invocations.</td> 
  </tr>
  <tr>
    <td>first+step</td>
    <td>For every syscall from the set, perform injections for syscall invocations number first, first+step, first+step+step, and so on. </td> 
  </tr>
</table>

For example:  
	
	# strace -e inject=read:error=1:when=2 ls
	execve("/usr/bin/ls", ["ls"], 0x7fff64d98c70 /* 21 vars */) = 0
	......
	read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0  \0\0\0\0\0\0"..., 832) = 832
	......
	read(3, 0x7ffec8c113a8, 832)            = -1 EPERM (Operation not permitted) (INJECTED)
	......

The `inject` takes effect when calling `read` in the second time.  

There are two more tracing events: `fault=set[:error=errno][:when=expr]` is similar as `inject`, and `kvm=vcpu` is used to print exit reason of `kvm` `vcpu` (requires kernel version `4.16` or later).