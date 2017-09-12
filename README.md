# Kernel-alarms
This is the final iteration of the kernel alarm module.

Even though some of our current computer systems can have huge amounts of memory installed, memory management is still 
a crucial aspect of Linux systems programming.  Especially with the rise in popularity of embedded devices that rely on Linux as their operating system, such as those in cellular phones, efficient memory management is as important as ever.  Although computer hardware is always improving, we still need to take into consideration the way we handle our limited memory resources, in order to decrease the amount of memory fragmentation that can otherwise occur.   
A system kernel can waste an enormous amount of processor cycles allocating, initializing, and freeing objects on order to both (1) keep them in a usable state, and (2) allow for the possibility of running a program that is larger than physical memory.   This waste of cycles can be neatly minimized by caching the structure of more frequently used objects so that they can be used without the need for freeing and re-allocating:  saving precious processor cycles and allowing for a more efficient memory management subsystem.  For this final incremental addition to our kernel timer project, we added a circular linked-list kernel data object, as a slab allocator.  
 A slab allocator is a method of pre-allotting a doubly-linked circular linked list of structures in memory, for the purpose of efficiently maintaining the basic structure of those objects in a constantly initialized state; bypassing the need to free and re-initialize a structure that will be re-used often. This efficient memory caching routine eliminates the fragmentation caused by the deprecated heap-style memory allocation routines which required careful memory reclamation routines in order to deal with the fragmentation caused by the multiple allocations and de-allocations of the multitudes of disparate object types that are constantly being swapped into and out of memory. 
The slab cache structure is created using: 
static struct kmem_cache *my_cachep; 
 
 
 
 
The user space component of this program loops for 300 seconds, printing the time every second.  As alarm signals are propagated by the kernel, my timer callback function gets called: 
        void my_timer_callback(unsigned long data) // KERNEL SPACE TIMER FUNCTION CALL 
        { 
        intret; 
          printk("kobject_tkp_myClock2: my_timer_callback called, send signal to pid %d \n", t[num_alarms-1]-> pid); 
          memset(&info,0,sizeof(struct siginfo));//clear the struct 
          info.si_signo=SIG_TEST;// set the signal number to our specified value:  44 
          info.si_code=SI_QUEUE; 
          info.si_int=alm_array[--num_alarms];//both decrease #/alarms and return that alarms TIME  
          ret=send_sig_info(SIG_TEST,&info,t[num_alarms]); 
          if(ret < 0) { 
            printk("kobject_tkp_myClock2: error sending signal\n"); 
          } 
        } 
This function sends the siginfo struct (struct siginfo info) to the user space.  
The user space can analyze the struct member contents and act accordingly:  in this case outputting the amount of time 
that the alarm had been set for, which is the value stored in the si_int member.



