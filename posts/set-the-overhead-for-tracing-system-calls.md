# Set the overhead for tracing system calls

`-O` option is used to set the overhead for tracing system calls, and the unit is microseconds (us). The following is excerpted from [manual](http://man7.org/linux/man-pages/man1/strace.1.html):  

> This is useful for overriding the default heuristic for guessing how much time is spent in mere measuring when timing system calls using the -c option. The accuracy of the heuristic can be gauged by timing a given program run without tracing (using time(1)) and comparing the accumulated system call time to the total produced using -c.  

Check the [source code](https://github.com/strace/strace/blob/e0f0071b36215de8a592bf41ec007a794b550d45/count.c#L131):  

		......
		ts_mul(&dtv, &overhead, counts[i].calls);
		ts_sub(&counts[i].time, &counts[i].time, &dtv);
		......

The overhead time is subtracted from the total consuming time, and this option just makes sure the measure of time spent on system calls to be more accurate.