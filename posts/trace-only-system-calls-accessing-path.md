# Trace only system calls accessing path

`-P path` option is used to set only tracing system calls which access the specified `path`. E.g.:  

	# strace -P /etc/ld.so.preload ls
	access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
	+++ exited with 0 +++

Only `access` system call is tracked.
