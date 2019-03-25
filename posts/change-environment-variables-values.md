# Change environment variables' values

`-E` option is used to change environment variables' values. Actually, `strace` just calls `putenv()` function (code is [here](https://github.com/strace/strace/blob/e0f0071b36215de8a592bf41ec007a794b550d45/strace.c#L1626)). Check following code:  

	# cat env_test.c
	#include <stdio.h>
	#include <stdlib.h>
	
	int main(void)
	{
	    char *p = NULL;
	    if (p = getenv("HG"))
	    {
	        printf("HG enviromental variable value is %s\n", p);
	    }
	    else
	    {
	        printf("HG enviromental variable value is not set\n");
	    }
	    return 0;
	}

Build and run it:  

	#  gcc env_test.c -o env_test
	# ./env_test
	HG enviromental variable value is /usr/bin/hg

Set `HG` to other value:  

	# strace -E "HG=git" ./env_test
	......
	write(1, "HG enviromental variable value i"..., 38HG enviromental variable value is git
	) = 38
	......

Remove `HG`:  

	# strace -E "HG" ./env_test
	......
	write(1, "HG enviromental variable value i"..., 42HG enviromental variable value is not set
	) = 42
	......
