# RTOS

## Concepts

### Foreground vs Background system

|Foreground | Background
|---------|----------
| ISR | Tasks

`Task Response Time`: in a single `while(1)` application, the max time taken for task execution is execution time of all other tasks in the system

the simple `sched` we implemented was not based on priority, it only organized background tasks in a single `while(1)`

---

### critical section

a block of code that can't be interrupted while executing and needs to be protected by `enable/disable interrupts`

---

### resource

anything that is used by SW (Tasks) such as: variables, I/O devices, peripherals, buffers, ...

`CPU registers` are not considered a resource as they are managed by `OS` when switching between tasks

---

### shared resource

Has 2 states:

- free
- taken

could be shared between:

- 2 tasks
- task & ISR

it is important to define the resources in a system, if they are shared and who they are shared by in order to implement the needed protection for the system to work correctly

once a shared resource is defined in the system, no task can use it unless it has exclusive access to it (mutual exclusion)

a typical process looks like:

    request resource -> own & use -> release resource

if the resource is not released, another task stays blocked on it and `starvation` occurs

---

### Task

piece of code that is written as a sole application with its `while(1)`, as if it exclucively uses 100% of system resources

`Task stack` is owned by the task
`TCB` is a struct owned by the `OS` as context switching requires storing and loading registers which must be done through assembly code

---

### context switching

![image](https://github.com/yasminEzF/Notes/assets/109252157/7c50c21e-c0e9-4633-8956-1034d031cc59)

A task is represented as a `Task Control Block (TCB)` for the `OS` and only knows the data in it for the task such as: priority, stack size, entry point, stack pointer and program counter.

When switching from `Task1` to `Task2`, `OS` performs a couple of steps:

- `PUSH` all registers using `SP1` to save `T1` context
- save `SP1` in `TCB1` and save `PC` for last location execution
- switch to `TCB2` to start restoring `T2` context
- restore `SP2` and `POP` all registers
- restore `PC` to resume `T2` execution from last location
- jump to `PC` to resume `T2`

context switching time depends on number of registers to be saved & restored, so having floating point registers in the system would increase time needed to context switch

---

### Multitasking

system that can gandle multiple tasks at once, where each task appears to be using `CPU registers` alone and owns `shared resources` exclucively

---

### Kernel

the `kernel` is the core of the `OS` which is responsible for:

- task handling
- task switching
- task sync
- task communication
- events (task managment)
- memory managment
- communication

---

### Task states

1. `deleted/dormant`: `stack` and `TCB` exist, but `OS` knows nothing about the task
2. `ready`: a queue/list of all ready task of lower priority than the one currently running
3. `running`: task that's currently using `CPU` which is of highest priority (task with context switched)
4. `waiting`: tasks that need a `resource` which is not currently available

![image](https://github.com/yasminEzF/Notes/assets/109252157/5379bc2a-dfdd-43bf-b60e-666c73506e4c)

at any `OS_fun()` call, a reevaluation of ready, running and waiting queues occurs to update task states

for interrupt handling, `OS_intEnter()` & `OS_intExit()` exist for context switching between tasks and interrupts

in a multicore system, multiple tasks can be running at the same time

`OS` creates `idle` task of lowest priority which consists of an empty `while(1)` in order to protect the system when no tasks are ready

---

### Scheduler/Dispatcher

`scheduler` doesn't swicth tasks, it only evaluates and determines which task is to be run (could be the one already running). Tasks reevaluation affects task latency (time taken for a task to transition from `ready` to `running`), so reevaluation occurs at `events` & `tick time`.

the `kernel` is responsible for:

- handling events
- running `scheduler` to determine task to be run
- run the chosen task (either already running or context switch to another)

in some `OS`, `scheduler` determines task to run and context switches too

---

### preemptive/nonpreemptive kernel

assuming that `T2` is of higher priority than `T1`

![image](https://github.com/yasminEzF/Notes/assets/109252157/55e6451d-53d3-47c1-956c-68dcdeec8a33)

`nenpreemptive kernel` is a `cooperative system` in which `non-reentrant functions` can be used with no protection. shared resource protection is not needed as well UNLESS owner task goes to `waiting` state and another requests the same resource.

max latency in a nonpreemptive system is current task execution time.

---

### re-entrant function

a reentrant function is one which would work correctly if interrupted, called again and then returns and executes correctly both times

---

### round robin

a system is configured to be either:

- `unique priority`: so for each priority level, a single task is assigned to that level and the existing scenario is that the `running` task is always of higher priority than the ones that are `ready`
- `shared priority`: multiple tasks can be assigned to the same priority level

depending on `OS`, it sorts the tasks to run according to either `OS_creationIndex` or first ready. `OS` is `non-preemptive` for tasks with the same priority level.

each task runs for `OS_timeslot` called `quanta` until it runs out so `OS` switches to next ready task or the task state changes to `waiting`, `deleted`, ...

---

### priority inversion

for `T1` and `T3` that share resource `R`, task priorities as `T3` > `T2` > `T1`, the following scenario takes place:

![image](https://github.com/yasminEzF/Notes/assets/109252157/cc5646a0-a472-4171-9947-984054919dd0)

1. `T1` runs & aquires `R`
2. `T3` ready & starts running (higher priority)
3. `T3` requests `R` which is taken, so `T3` moves to `waiting` state
4. `T2` ready & runs (higher priority) and `T1` moves to `waiting` state
5. `T2` done and `T1` moves from `waiting` to `running`
6. `T1` releases `R` & `T3` aquires it and moves from `waiting` to `ready` to `running` (higher priority)
7. `T3` releases `R` & continues running till done
8. `T1` continues running till done

`inversion time` is the time from `T3` requesting the resource `R` until it actually owns it

---

### priority inheritance

inheriting priority happens at `resourceRequest();` of higher task only to avoid priority inversion between `T1` and `T2` (when needed)

![image](https://github.com/yasminEzF/Notes/assets/109252157/188c2fce-3b35-4e02-912f-38175288894c)

if `round robin` is activated & `T4` exists with same priority as `T3`, inversion time would increase

---

### priority cieling

upgrading lower task priority to higher task priority + 1, decreases inversion time as number of tasks that can interrupt it decreases
