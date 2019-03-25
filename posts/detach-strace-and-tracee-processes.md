# Detach strace and tracee processes

`-D` option is used to detach `strace` and `tracee` processes. E.g.:  

	# strace -D ./dead_loop
	strace: Process 19816 attached
	......

Open another terminal to check the process relationship:  

	# ps -ef | grep dead
	root     19816 19684  0 09:28 pts/0    00:00:00 ./dead_loop
	root     19820     1  0 09:28 pts/0    00:00:00 strace -D ./dead_loop

You can see now `dead_loop` is not child process of `strace`.