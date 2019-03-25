# Suppress attach/detach/exit status messages

`-q` option is used to suppress attach/detach process message. Compare following outputs:  

a) Without `-q` option:  

	# strace -p 444
	strace: Process 444 attached
	restart_syscall(<... resuming interrupted nanosleep ...>) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7ffc026d31c0) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7ffc026d31c0) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, ^Cstrace: Process 444 detached
	 <detached ...>
	
b) With `-q` option:  

	# strace -q -p 444
	restart_syscall(<... resuming interrupted nanosleep ...>) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7ffc026d31c0) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7ffc026d31c0) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, ^C <detached ...>


`-qq` option will also suppress process exit status message. Compare following outputs:  

a) Without `-qq` option:  

	# strace ls
	......
	exit_group(0)                           = ?
	+++ exited with 0 +++

b) With `-qq` option:  

	# strace -qq ls
	......
	exit_group(0)                           = ?
