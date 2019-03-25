# Strace little book
I like researching debugging techniques, so I decide to write this booklet to introduce [strace](https://strace.io/). The following is the official definition of `strace`:  

> strace is a diagnostic, debugging and instructional userspace utility for Linux. It is used to monitor and tamper with interactions between processes and the Linux kernel, which include system calls, signal deliveries, and changes of process state.

> System administrators, diagnosticians and trouble-shooters will find it invaluable for solving problems with programs for which the source is not readily available since they do not need to be recompiled in order to trace them.

> The operation of strace is made possible by the kernel feature known as ptrace.  

In one word, `strace` helps you know a process's activities between user-space and kernel-space. Let's check a simple example to get first impression of `strace`:  

    # strace ls
    execve("/usr/bin/ls", ["ls"], 0x7ffe7727eec0 /* 20 vars */) = 0
    brk(NULL)                               = 0x560a32cda000
    arch_prctl(0x3001 /* ARCH_??? */, 0x7fff167898f0) = -1 EINVAL (Invalid argument)
    access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
    openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
    fstat(3, {st_mode=S_IFREG|0644, st_size=98317, ...}) = 0
    mmap(NULL, 98317, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7face703c000
    close(3)                                = 0
    openat(AT_FDCWD, "/usr/lib/libcap.so.2", O_RDONLY|O_CLOEXEC) = 3
    ......

`strace` outputs all the syscalls' names, arguments, and return values. Very cool, isn't it? OK, let's begin our journey now.
