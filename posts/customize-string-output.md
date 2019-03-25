# Customize string output

`-x` option makes non-ASCII strings outputted in hexadecimal format (default is octal):  

	# strace -x ls
	......
	read(3, "\x7f\x45\x4c\x46\x02\x01\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x03\x00\x3e\x00\x01\x00\x00\x00\x20\x20\x00\x00\x00\x00\x00\x00"..., 832) = 832

`-xx` makes all strings outputted in hexadecimal format:  

	# strace -xx ls
	......
	read(3, "\x7f\x45\x4c\x46\x02\x01\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x03\x00\x3e\x00\x01\x00\x00\x00\x20\x20\x00\x00\x00\x00\x00\x00"..., 832) = 832


Furthermore, `-s strsize` changes the maximum print size of string (default value is `32`). Compare the following outputs: 

(1) Using default value:  

	# strace ls
	......
	read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0  \0\0\0\0\0\0"..., 832) = 832
	......

(2) Double the size:  

	# strace -s 64 ls
	......
	read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0  \0\0\0\0\0\0@\0\0\0\0\0\0\08R\0\0\0\0\0\0\0\0\0\0@\08\0\t\0@\0\26\0\25\0"..., 832) = 832
	......

Please note that filenames are not considered strings and are always printed in full.