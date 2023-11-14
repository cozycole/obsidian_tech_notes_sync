![Source](https://cpu.land/slice-dat-time)

## Hardware Interrupts

Software interrupts voluntarily give up control of the executing userland program to the kernel. Hardware interrupts force the userland program into kernel mode to execute OS multitasking code. 

OS schedulers use *timer chips* to trigger hardware interrupts for multitasking.
1. Before jumping to program code, the OS sets the timer chip to trigger an interrupt after some period of time.
2. The OS switches to user mode and jumps to the next intrusction of the program.
3. When the timer elapses, it triggers a hardware interrupt to switch to kernel mode and jump to OS code.
4. The OS can now save where the program left off, load a different program then back to step 1.

This is *preemptive multitasking*.

## Timeslice

A timeslice is the duration an OS scheduler allows a process to run before preempting it. There are algorithms that take into account process priority and deadlines that optimally choose certain length timeslices for each process.

**IMPORTANT**: Every time the OS preempts a process it needs to load the program's saved execution context which is accomplished by telling the CPU to use a different *page table*, a mapping from virtual to physical addresses.

## Aside - Kernel Preemptablilty

Kernel code itself can be preempted which defends against slow kernel process times. 