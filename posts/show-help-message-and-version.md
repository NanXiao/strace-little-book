# Show help message

As other `*NIX` commands, `-h` option shows a concise help message of `strace`:  

	# strace -h
	usage: strace [-CdffhiqrtttTvVwxxy] [-I n] [-e expr]...
	              [-a column] [-o file] [-s strsize] [-P path]...
	              -p pid... / [-D] [-E var=val]... [-u username] PROG [ARGS]
	   or: strace -c[dfw] [-I n] [-e expr]... [-O overhead] [-S sortby]
	              -p pid... / [-D] [-E var=val]... [-u username] PROG [ARGS]
	
	Output format:
	  -a column      alignment COLUMN for printing syscall results (default 40)
	  -i             print instruction pointer at time of syscall
	  -k             obtain stack trace between each syscall
	...... 

`-V` shows version:  

	# strace -V
	strace -- version 4.26
	Copyright (c) 1991-2018 The strace developers <https://strace.io>.
	This is free software; see the source for copying conditions.  There is NO
	warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
	
	Optional features enabled: stack-trace=libunwind stack-demangle m32-mpers mx32-mpers
