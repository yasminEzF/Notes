# OSEK Standard

Automotive RTOS standard

## Components

1. counter
2. alarm
3. tasks
4. events
5. resources
6. ISR
7. Hooks

counter triggers alarm that gives an event

Task is a function with timing parameters

Task activated by alarm, ISR or Task

Resources are HW (LCD, SPI, CAN, ...) or SW (global variable, ...)

![327968324-5ab3cf4a-d616-42e7-882b-df12ed4f0d7a](https://github.com/yasminEzF/Notes/assets/109252157/187c9fd0-7be5-4b4e-8705-8c1783ddf815)

## 1. Tasks

OSEK standard is priority based, if same priority ,then it is first come first served.

### 1.1. Basic Task

basic tasks are self terminating

![Screenshot 2024-05-04 212931](https://github.com/yasminEzF/Notes/assets/109252157/19aa816d-f282-4bb1-b527-ecbc00fd121d)

### 1.2. Extended Task

![Screenshot 2024-05-04 213402](https://github.com/yasminEzF/Notes/assets/109252157/ee054c39-b56c-473e-b8d7-552cba71a4d5)

### preemptive tasks

priority 3 is higher than priority 2, once a higher priority task is ready -if the running task is preemptive- lower priority task is interrupted and switched to ready state, while the higher priority task runs.

|   |Task 2 | Task 3
-------|-------|-------
priority| 2 | 3 
t1|running|ready
t2|ready|running


### task definition using OIL language

```C
TASK task1{
    TYPE        //basic or extended
    PRIORITY    // 3 > 1
    SCHEDULE    //preemptive or not
    ACTIVATION  //max no. of activations @ ready and still not running
    AUTOSTART   //at system statrtup, called by OS
    RESOURCE    //
    EVENT       //
    STACK SIZE  //
}

```

## 2. Resource

- HW
- SW

### resource protection

for a shared resource `u32 z;`, Task 1 reads, Task 2 writes

|T1 | T2
|--|--
|`getResource(z)` | `getResource(z)`
|`z = 150` | `y = z`
|`releaseResource(z)` | `releaseResource(z)`

### priority inversion

for a shared resource between Task1 & Task3, with assigned priorities as `T3` > `T2` > `T1`

![Screenshot 2024-05-04 224201](https://github.com/yasminEzF/Notes/assets/109252157/39376d5b-b6f5-4cf7-8791-9022efe97c03)

### deadlock

```C
T1.getResource(r2)
T2.getResource(r1)
T2.getResource(r2)  //failed
T1.getResource(r1)  //failed
/* Both tasks are stuck in waiting state */
```

### priority inheritance

inheriting priority could be at resource get of lower task or at resource request of higher task

![Screenshot 2024-05-04 225838](https://github.com/yasminEzF/Notes/assets/109252157/a9cb809b-5e0a-4e05-b12d-965ebe5268ea)

### priority cieling

upgrading lower task priority to higher task priority + 1, decreases inversion time

### Some OSEK API's

1. `chainTask`: terminates current task & activates next one.

    ```C
    Task1(){
        chainTask(Task2);
    } 
    ```

2. `applicationMode`: define for OS which mode to start on & configure parameters accordingly (already set up in OIL file).

- Development mode
- Production mode
- Preproduction mode

### Scheduling points

|  | Preemptive | Nonpreemptive
|---|---|---
Activate Task | T | F
Terminate Task | T | T
Wait event | T | T
Chaining task | T | T
get resource  | F | F
release resource | T | F
sched(); | T | T
ISR | T | F

## 3. Hooks

they are functions called by OS, implemented by user

### 3.1. pretask hook

### 3.2. posttask hook

both pre & post task hooks are used for testing & debugging

### 3.3. startup hook

```c
power on
startup code
main();
OS_start();
startupHook();
```

### 3.4. shutdown hook

called at `OS_shutdown()`

### 3.5. error hook

implemented for each error id

## 4. Counters

they run over a timer peripheral

### parameters

`tick per base = 10`

counter = 10 timer overflows -> OS = 1 tick

`max allowed value = 25`

timer overflow at 25

`min cycle = 10`

no alarm can be set for less than 10 cycles

## 5. Alarms

they run based on counters

### 5.1. absolute time

```c
absoluteTime(alarm, start, cycle time)
```

- `alarm`: alarm name
- `start`: value of counter to start alarm
- `cycle time`: time for alarm to count

### 5.2. relative time

```c
relativeTime(alarm, increment, cycle time)
```

`increment`: counts to wait from call to start alarm counting

## 6. Events

events have a single owner, so if task 1 waits on event 1, Task 2 can't wait on event 1 as well.

What can set an Event?

- alarms
- other Tasks
- ISR's

```c
EVENT event1{
    MASK    //an 8-bit value that represents event fields
}
```

| E7 | E6 | E5 | E4 | E3 | E2 | E1 | E0
|---|---|---|---|---|---|---|---
|0|1|0|0|1|0|0|1

in this example, the task uses events 0, 3 & 6

## 7. ISR

| category 1 | category 2
|---------|----------
|`interrupt` | `interrupt`
| `ISR();` | `OS`
|  | `ISR();`
| less latency | more latency
| direct call | called through OS
| | SW priority support (0 > 7)
