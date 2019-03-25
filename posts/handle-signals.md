# Handle signals

`-I interruptible` option is used to handle signals. The `interruptible`'s value can be `1` ~ `4`:  

<table>
  <tr>
    <th>Value</th>
    <th>Meaning</th> 
  </tr>
  <tr>
    <th>1</th>
    <th>Don't block any signal</th> 
  </tr>
  <tr>
    <th>2</th>
    <th>Block fatal signals while decoding syscall, and this is default behaviour.</th> 
  </tr>
  <tr>
    <th>3</th>
    <th>Block fatal signals, and this is default behaviour when using '-o FILE PROG' option.</th> 
  </tr>
  <tr>
    <th>4</th>
    <th>Block fatal signals and SIGTSTP (^Z), and this is useful when not want '-o FILE PROG' option to stop on ^Z.</th> 
  </tr>
</table>  

The fatal signals include `SIGHUP`, `SIGINT`, `SIGQUIT`, `SIGPIPE` and `SIGTERM`. Check following example:  

	# strace -I 1 ./dead_loop
	......
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7fff0a0d90f0) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7fff0a0d90f0) = 0
	nanosleep({tv_sec=1, tv_nsec=0}, 0x7fff0a0d90f0) = 0
	......
Open another terminal and input following command:  

	# kill -s INT `pidof strace`

You will find `strace` process is killed. If you run `strace` using "`-I 3`", you will find the `SIGINT` will not take effect when using above command:  

	 # kill -s INT `pidof strace`

