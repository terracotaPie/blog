---
title: "Kernel module monitoring syscalls"
date: 2017-08-29T19:13:57-04:00
draft: false
---
So in one the OS classes we had to write a kernel module that intercepts system calls of choice. For that matter we were introduced to the concept of system call table. There is a good description [here](http://www.linfo.org/system_call.html). We start with a snippet taken from [tldp](http://tldp.org/LDP/lkmpg/2.6/html/hello2.html#AEN232).

```c
#include <linux/kernel.h>	/* Needed for KERN_INFO */
#include <linux/init.h>		/* Needed for the macros */

extern void* sys_call_table[];
/* Apparently kernel doesn't export the address starting 2.6
 * You can find solution involving reading "System.map"
 */

static int init_function(void) {
    // Make the sys_call_table r/w
    set_addr_rw((unsigned long) sys_call_table);
    // Save original systems calls
    orig_custom_syscall = sys_call_table[MY_CUSTOM_SYSCALL];
    orig_exit_group     = sys_call_table[__NR_exit_group];
    // Hijack both system calls
    sys_call_table[MY_CUSTOM_SYSCALL] = (unsigned long *) my_syscall;
    sys_call_table[__NR_exit_group] = (unsigned long *) my_exit_group;
    // Revert the sys_call_table to read only
    set_addr_ro((unsigned long) sys_call_table);
	return 0;
}

static void exit_function(void)
{
    // Restore original syscall
    set_addr_rw((unsigned long) sys_call_table);
    sys_call_table[MY_CUSTOM_SYSCALL] = (unsigned long *) orig_custom_syscall;
    sys_call_table[__NR_exit_group] = (unsigned long *) orig_exit_group;
    set_addr_ro((unsigned long) sys_call_table);
}

module_init(init_function);
module_exit(exit_function);
```

Things are pretty straight-forward here. We will be controlling the functionality of our module using hijacked syscall under index 0 - [sys_restart_syscall](https://syscalls.kernelgrok.com/). The reason for overwriting this particular one is that it should leave the system mostly functional. During initialization we save original system calls addresses and replace them. Note that we also replacing exit group syscall, I will explain in detail why we are doing this. On exit we should restore our system to the original state by restoring saved addresses earlier. Note that we need to set the read/write flags when we alter the table - I will leave that for you.

Our `my_syscall()` will control the behaviour of our monitor:

  * REQUEST_SYSCALL_INTERCEPT to intercept the 'syscall' argument
  * REQUEST_SYSCALL_RELEASE to de-intercept the 'syscall' argument
  * REQUEST_START_MONITORING to start monitoring for 'pid' whenever it issues 'syscall'
  * REQUEST_STOP_MONITORING to stop monitoring for 'pid'



```c
asmlinkage long my_syscall(int cmd, int syscall, int pid) {
    printk( KERN_INFO "this are the args %d %d %d\n",cmd,syscall,pid);

    switch (cmd)
    {
        case REQUEST_SYSCALL_INTERCEPT:
            return __intercept(syscall,pid);
        case REQUEST_SYSCALL_RELEASE:
            return __release__(syscall,pid);
        case REQUEST_START_MONITORING:
            return __start(syscall,pid);
        case REQUEST_STOP_MONITORING:
            return __stop(syscall,pid);
    }
    return 0;
}
```
With the information given you can figure out how to implement those 4 methods. We were given nifty structs to mantain tabs on the intercepted pids/syscalls
```c
struct pid_list {
	pid_t pid;
	struct list_head list;
};


/* Store info about intercepted/replaced system calls */
typedef struct {

	/* Original system call */
	asmlinkage long (*f)(struct pt_regs);

	/* Status: 1=intercepted, 0=not intercepted */
	int intercepted;

	/* Are any PIDs being monitored for this syscall? */
	int monitored;
	/* List of monitored PIDs */
	int listcount;
	struct list_head my_list;
}mytable;

/* An entry for each system call */
mytable table[NR_syscalls+1];
```

So what happends if the process exits while being monitored? Yes that's why we need custom `exit_group` system call. When process decides to exit we remove it from our monitoring table and call origin `exit_group`.

I hope I have given you enough information for you to be able to write it on your own. Some of the starter code provided is the credit of our awesome Professor [Bogdan Simion](http://www.cs.toronto.edu/~bogdan/)