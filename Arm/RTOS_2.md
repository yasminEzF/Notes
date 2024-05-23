# RTOS Lec 2

Dispatcher = scheduling + context switching.

Shared resource must be exclucively accessed, it can be shared by:

1. Task + ISR
2. Task + Task

Shared resources could be SW resources (Counter variable) or HW resources (I/O device such as LCD or printer)

## Shared resource protection

### Shared resource between Task + ISR

It is not logical to protect resource in ISR as it is interrupt handler so the task won't be able to interrupt it, also an interrupt occurence means it needs to signal some event so it must access the resource to indicate this signal. If the interrupt occurred once and couldn't access the resource (no action take), no info can be provided for when this interrupt might occur again as it doesn't go to waiting state unlike tasks.

`critical section` is the shared resource, where only way of protecting it in this scenario is to disable interrupts before accessing and enabling it again after exit.

Disabling interrupts kills OS so this isn't really a valid option, so OS provides API's to use such as `criticalSection_enter` and `criticalSection_exit`.

`critical sections` must be small in size in order to not block other functionalities of the system (ex: setting flag, updating counter,...)

### Shared resource between Task + Task

As mentioned before, disabling interrupts is not an option as it kills the OS entirely. Another option provided by OS `stopScheduling` or `lock/stopKernel` API's to change OS to non-preemptive and protect task's access to shared resource while also allowing interrupts and handlers to execute as normal. At the end of the critical section, other API's are used to return to normal execution style using `startScheduling` or `unlock/startKernel`.

## Semaphores

`Semaphores` are used for task sync, event handling and resource protection. It's main benefit is that it is handled by `OS`. `Semaphore` is a counter that represents number of users that can use a single resource at a time. `Binary Semaphore` means that only one user can own a resource at a time.

`OS` provides some API's to handle semaphores such as `init/create`, `take/pend/wait` and `signal/post/release`.


