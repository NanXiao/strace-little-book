# Show file descriptor related information

`-y` option can be used to print file path associated with each file descriptor. Compare the following outputs:  

a) Without `-y` option:  

	# strace ls
	......
	openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
	fstat(3, {st_mode=S_IFREG|0644, st_size=98309, ...}) = 0
	......
 
b) With `-y` option:  

	# strace -y ls
	......
	openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3</etc/ld.so.cache>
	fstat(3</etc/ld.so.cache>, {st_mode=S_IFREG|0644, st_size=98309, ...}) = 0
	......

Furthermore, `-yy` option can be used to print protocol specific information associated with socket file descriptors, and block/character device number associated with device file descriptors. Compare following outputs:  

a) Without `-yy` option:  

	# strace ip
	......
	socket(AF_NETLINK, SOCK_RAW|SOCK_CLOEXEC, NETLINK_ROUTE) = 3
	setsockopt(3, SOL_SOCKET, SO_SNDBUF, [32768], 4) = 0
	......

	# strace ls
	......
	fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(0x88, 0x1), ...}) = 0
	......


b) With `-yy` option:  

	# strace -yy ip
	......
	socket(AF_NETLINK, SOCK_RAW|SOCK_CLOEXEC, NETLINK_ROUTE) = 3<NETLINK:[29833]>
	setsockopt(3<NETLINK:[29833]>, SOL_SOCKET, SO_SNDBUF, [32768], 4) = 0
	......

	# strace -yy ls
	......
	fstat(1</dev/pts/1<char 136:1>>, {st_mode=S_IFCHR|0620, st_rdev=makedev(0x88, 0x1), ...}) = 0
	......
