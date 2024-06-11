# Process Syncronization

Two tasks can typically communicate through a shared resource (shared memory). The section of code that accesses this memory will be called `critical section (CS)`, while the rest of the code will be called `non-critical`.

![image](https://github.com/yasminEzF/Notes/assets/109252157/2ba1fd91-8f0e-483f-b442-209f1ec119e7)

in an example where `counter` is a shared resource between `T1` and `T2`, which are preemptive, the scenario is as follows:

1. `T1` starts and takes a copy of `counter` with value `0`
2. `T2` interrupts and takes a copy of `counter` with value `0` too
3. `T2` decrements to `-1` and stores the value
4. `T2` is done, so `T1` returns to continue execution and increments old copy of `counter` to `1`
5. `T1` stores `counter` value as `1` instead of `0` (data corruption)

![image-4](https://github.com/yasminEzF/Notes/assets/109252157/2bde596f-c835-4d0b-8068-65bb9981b17c)

for every `CS` in code, there exists an `entry section` and `exit section` for protection and to ensure that the code works and accesses shared resources correctly and safely.

![image](https://github.com/yasminEzF/Notes/assets/109252157/723470ac-f3db-49d7-a5c4-be37af7f68be)

## methods for sync

1. busy wait
2. non-busy wait (generic method)

## Evaluation parameters for each method

1. **Mutual Exclusion:** No 2 processes are executing `CS`'s at the same time.

1. **Progress:** No task is blocked from entering a `CS` by a task that's in a `non-critical section`.

1. **Bounded limit:** Limiting number of `CS` re-entry times per task to avoid endless `CS` entry by a single task and infinite blocking for the others (starvation)

## Busy Wait Methods

### 1. Lock Variable

![image-5](https://github.com/yasminEzF/Notes/assets/109252157/2ba71601-321c-40ae-8f12-050542d8d2a0)

|Evaluation parameter | Status | Cause
|---------|----------|---------
|**Mutual Exclusion** | fail `:(` | `lock` is a variable treated like first scenario, so an interruption can occur between `while` and locking `lock = 1` and data corruption of `lock` which leads to multiple tasks at `CS` at the same time
|**Progress**| success `:)` |
|**Bounded limit** | fail `:(` | no limit to each task's entry

|Pros | Drawbacks
|---------|----------
| HW independent | busy wait method
| handle multiple processes |

### 2. Test & Set (T&S)

load and store instructions are combined into 1 new atomic assembly instruction (no interruption can occur in between). This allows the reading of `lock` flag and locking it just incase until condition evaluation complete.

![image-6](https://github.com/yasminEzF/Notes/assets/109252157/183ba5ed-e2e9-4489-a58b-9c7033ddfa0d)

|Evaluation parameter | Status | Cause
|---------|----------|---------
|**Mutual Exclusion** | success `:)` |
|**Progress**| success `:)` |
|**Bounded limit** | fail `:(` | no limit to each task's entry

|Pros | Drawbacks
|---------|----------
| multiple processes | busy wait method
|  | needs HW support, if not supported can be implemented by reordering assembly instructions

`T&S` is supported by most HW, by locking data bus.

### 3. Strict Alternative

Tasks take turns executing critical sections, on condition that a process ca't execute it's `CS` unless the other process took its turn.

![image-7](https://github.com/yasminEzF/Notes/assets/109252157/2fd2c30a-3f10-4308-8c6b-f8f2c61421c2)

|Evaluation parameter | Status | Cause
|---------|----------|---------
|**Mutual Exclusion** | success `:)` |
|**Progress**| fail `:(` | `T2` can't execute unless `T1` executed once first
|**Bounded limit** | success `:)` |

|Pros | Drawbacks
|---------|----------
| HW independent | busy wait method
|  | 2 processes only

### X. Disable all Interrupts

not really used as a method because disabling interrupts would mean killing the `OS`. (tasks are non-preemptive?)

![image-8](https://github.com/yasminEzF/Notes/assets/109252157/6c298406-2428-4afb-8614-8da49e2a89ce)

### 4. Interested Variable

![image](https://github.com/yasminEzF/Notes/assets/109252157/b75ff786-bd5a-48a6-bd70-564f4f67af4d)

|Evaluation parameter | Status | Cause
|---------|----------|---------
|**Mutual Exclusion** | success `:)` |
|**Progress**| fail `:(` | Due to Deadlock scenario where none of the tasks are in critical sections but they are blocking each other.
|**Bounded limit** | success `:)` | achived because `T2` sets its `interest2` variable and blocks `T1` from re-entering `CS` before it.

`Deadlock` occurs in case that `T1` set `interest1` variable then `T2` set `interest2` variable, so that both tasks get stuck while none of them is in a critical section.

|Pros | Drawbacks
|---------|----------
| multiple processes | busy wait method
|  | causes Deadlock so it isn't used

### 5. Peterson

```c
 entry ( processNum ) {
  int other = 1 - processNum;                               // 1
  interest [processNum] = True;                             // 2
  turn = processNum;                                        // 3
  while( interest [other] = True && turn == processNum );   // 4
 } 

  exit ( processNum ) {
  interest [processNum] = False;
 } 
```

Entry section executes as following for each process, the process identifies other processes that exist along with it and sets its own `interest` variable as `True`, it then reserves its turn to enter its critical section, then waits for as long as the other process' `interest` variable is set and the turn is owned by it. Once the other process is done, it will execute Exit section which will clear its `interest` variable, allowing the first process to break out of the while and enter its critical section.

![image-2](https://github.com/yasminEzF/Notes/assets/109252157/5f68695c-3be2-4b6a-8a90-590a1299a74c)

assuming `P0` and `P1` processes exits, their interest variables initialized as `False`:

- `P0` executes first 2 lines of Entry section, so it identifies the presence of `P1` and sets its `interest` variable.
- `P1` executes first 3 lines and does the same as `P0` but sets turn to be `P1`.
- `P0` executes and sets turn to be `P0` and gets stuck waiting.
- `P1` checks condition to wait and enters critical section as normal.

|Evaluation parameter | Status | Cause
|---------|----------|---------
|**Mutual Exclusion** | success `:)`
|**Progress**| success `:)` | Blocking occurs when a process sets turn after another process already claimed it (re-check example `P1` set trun variable first)
|**Bounded limit** | success `:)`

|Pros | Drawbacks
|---------|----------
| HW independent | busy wait method
| multiple processes | 

## Non-Busy wait methods

no `while` loops and no waiting occurs, but simply `OS` handles each process and moves them between waiting, ready & running states. Processes execute in `user` mode, make `system calls` and execute them in `kernel` mode (non-preemptive).

![image-3](https://github.com/yasminEzF/Notes/assets/109252157/376bbcc8-52ce-48ae-9f98-0b36330557a2)

### 1.1 Binary Semaphore



### 1.2 Counter Semaphore
