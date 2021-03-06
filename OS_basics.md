# Some notes on OS basics
---

# Processes vs. Threads

In a broad sense we can say, a process consist of following things(using stack implementation):
* Stack(registers)
* Code
* Datafiles

This comparision is based on **multitasking** processor.
To deal with multiple processes we have to bring concept of multiple CPUs(but it is genarally one)
So, we can create multiple processes or multiple threading for processes.

## Processes

Some points for **Processes** process:

* ***System calls are involved in this process.***
	- Process like `fork()` which are used to clone the main process(create child) to do other task.
	- Basically using kernel(OS) to call fork() to generate a new process to handle the task.
	- This is basically a **kernel-level process**.
* ***OS treats different process differently.***
	- Basically, every process called have a different `PID` with a common `PPID`.
* ***Different process have different copies of*** `data, files & code`.
	- So, a new process will clone everything which is told above of what a process consists.
	- Also, if `n fork()`(cloning process) are called then total ((2*n) - 1) child process will be created.
	- So, there will be total of **2 * n** process.
* ***Context Switching is slower.***
	- So, if a context switching happens from a process to another then it has to save alot of values.
	- These values are stored in `PCBs` with the help of OS which is very much time consuming.
* ***Blocking a process will not block another process.***
	- Let's say, parent process is blocked(maybe due to some I/O demand), so it's in waiting state(block state).
	- At this time, child processes are not blocked. They are running independentely.
	- Same thing for one child process to another.
* ***So, this is independent.***

Here, is explanation of fork:
![fork1](images/OS_basics_img/fork2.jpg)

more on fork()
![fork2](images/OS_basics_img/fork1.jpg)

## Threads

Some points for threads:

* ***There is no system call involved.***
	- So, basically `API`(application program) creates multiple threads(depends).
	- So, this is a **user-level process**.
* ***All user-level threads treated as single task for OS.***
	- So, all threads related to a task have a single `PID`.
* ***All threads of a process(task) share all the `data & code`.***
	- They only have different combinations of stack(registers).
* ***Context Switching is faster.***
	- The only switching which happens here is basically of stack(registers/addresses).
	- This is not very time consuming.
* ***Blocking a thread will block entire process.***
	- Let's say there a three threads _t1, t2 and t3_ in a process.
	- As said, they will share _data & code_ but they will have independent registers.
	- Now, if `t2` demands for I/O, this demand will reach to kernel(OS).
	- So, kernel will block the process, because kernel doesn't knows that this particular process consists of 3 threads.
	- Because, these threads are created as **user-level** not **system-level** process.
* ***Threads are interdependent.***

# Types of Threads:

* User Level Threads
* Kernel Level Threads

## User Level Threads:

* User level threads are managed by the User level library.
	- Like if we are writing a C program and use `pthread.h` which is library for creating threads.
	- So, functionalities like `pthread_create, pthread_join` are user level.
	- Time consumin is very low.
* User level threads are typically fast.
	- These are created by _APIs_, so OS is only responsible for execution and nothing else.
* Context switching is faster.
	- Context switching only happen in stack(registers set) for each thread.
	- As the user-level threads share `code & data.`
	- This doesn't require OS to interfere which makes it faster.
* If one user level threads perform blocking operation then entire process will be blocked.
	- So using different models we tell kernel that we had used multi-threading in this process

## Kernel Level Threads:

* Kernel level threads are managed by OS.
	- These are created using **system calls**
	- This will take more time
* Kernel leve threads are slower.
	- In time consuming meter, this is almost similar to creating a new process(child).
* Context switching is slower.
	- Previously told.
	- We can tell following order for time consuming for context switching:
		+ Processes > Kernel level threads > User level threads
* If one kernel level thread blocked, it doesn't affects others.

# User Mode vs. Kernel Mode:

Let's take an example, suppose I opened a text editor in linux system and typed a program to read a file and write something into it.
Now, data is stored in _storage drive(HDD, SSD etc)_. So, after `user-process executing` a `get system call` will happen.
Like in program `Read()` will happen.

Now, as Read() will happen, a **trap()**(interrupt) is set up. Now, I as soon as we shift from user mode to kernel mode, mode bit changes from **1 to 0**.

Yes, Mode bit for **User mode** is 1.
Mode bit for **Kernel mode** is 0.

Now, system call will be executed and after that we'll come back to user mode, because user works in User mode.
And, also mode bit changes to 1 from 0.

Let's take another example, there's a C program in which it is written to add two number.
Now, this doesn't require kernel to process. But, if wrote `printf()` to show output in monitor, it requires kernel mode.

So, _user mode_ and _kernel mode_ in combination is called **Dual Mode.**

# Multilevel Queue Scheduling

This type of scheduling tells that for each type of process there should be a different **ready queue**.
Before, it we used a single ready queue for all types of process.

The processs could be of following types:
* Highest Priority -> system process(interrupts etc.)
* Medium Priority -> interactive process(movie watching, programming etc.)
* Lowest Priority -> background process

So, every type of process have their own ready queue.

Now, scheduling algorithms could may or may not be different for each type of process.
Like, for above given three types of process, OS could use
* Round Robin
* Shortest Job First
* First Come First Serve

They could be same also. Now, these are feeded to CPU.
Now, there's a problem also. If OS had too many _system processes_, then processes with medium and low priority will _starve_.
Solution could be **Multilevel Feedback Scheduling**.

# Multilevel Feedback Queue Scheduling

As seen above, if lowest priority process gives feedback and OS upgrade it, the starvation could be avoided.

Two things to take care of here:
* Lowest priority process should be upgraded.
* Highest priority process should not be interrupted.

Here's a screenshot to clear out.
![Multilevel Feedback Queue Scheduling](images/OS_basics_img/mqfs.jpg)

# Process Synchronization

In multiprocessing environment, another process should not start till first process is complete.
This is called Process Synchronization.

Process Synchronization mainly deals with two types of process:-
* Co-operative Process
* Independent Process

## Co-Operative Process -
Processes whose execution affects another process. This happens because of sharing.
They can share _variable, memory(buffer), code, resources(CPU, printers etc.)_.

## Independent Process -
Process whose execution doesn't affects another process. So, it's obvious that they have nothing in common.

# Race Condition:
Here is the image captured explaining race condition:
![race codition](images/OS_basics_img/race_cond.jpg)

So, if co-operative process isn't synchronized properly, they can create some problem.

# Critical Section
It is a part of the program a shared resource is accessed by the various process.
These processes are co-operative. Also, these are con-current process.

So, if two concurrent programs have something in common, then those things will be put in critical section.
Non-common things are put in non-critical section.

Critical section is a place where _shared variables, resources_ are placed.

If a program is accessing a critical section then another program should not access it.
If it happens then **Race Condition** will occur. To avoid _race condition_, synchronization is done in these processes in critical section.

Different methods like **Semaphore, monitor, lock-variables etc.**.

So, we define a _entry section_ before critical section in concurrent programs.
If condition satisfies then critical section can be accessed.

There can also be a **exit section** after critical section in concurrent process.

# Synchronization Mechanism

4 conditions for any synchronization method:
* mutual exclusion
* progress
* bounded wait
* No assumption related to hardware or speed

First two rules are mendatory for process synchronization.

Let's there are two process **P1 and P2**. If P1 is in critical section then P2 must not get to entry section.
If this is achieved then **Mutual Exclusion** is achieved.

Let's say, there is not any process executing common code of critical section at give particular time.
That means, critical section is currently empty.
Now, let's say _P1_ is intersted to go into critical section but _P2_ is blocking P1
because P2's entry section may contain such code which code block P1 to access critical section or vice versa
then there isn't **Progress.**
So, _progress_ is must for synchronization.

Now, let's say _P1_ accessed critical section and _P2_ not.
Again _P1_ enters critical section and P2 doesn't, still no problem.
But, if this continuous for **infinite** times, means
> P1 -> infinite		P2 -> 0
then this condition is known as **Unbound Wait** condition.

If, it is like
> p1 -> 10				P2 -> 1
then it is **Bound wait** condition. Means, P2 still gets access of critical section.

Now, let's say someone gave a mechanism which have some conditons like this:
- it'll work on 2 Ghz processor fastly
- it'll require 32bit system.

Then this is not an ideal mechanism to achieve _synchronization_.
All the mechanism should be:
- portable
- cross-plateform
- kind-of universal
- easily portable
- easily modifiable

So, primary conditions for synchronization mechanims are:
- Mutual Exclusion
- Progress

And secondary condtions are
- Bound
- No assumption related to hardware or speed

# Classical Problem Of Synchronization

* Classical problems synchronization:
	* Producer & Consumer(Bounded-Buffer) Problem
	* Printer Spooler Problem
	* Readers and Writers Problem
	* Dining-Philosophers Problem
	* Sleeping Barber Problem

## Producer-Consumer problem

Standard problem for multi-process for process synchronization. It's generated in co-operative processes.

Two types of processes are there:
* Producer process - produce the information
* Consumer process - consume the information produced by producer

* Producer process produces information that is consumed by a Consumer process.
* The information is passed from the Producer to the Consumer via a **buffer**.

Two types of buffers can be used:
* `unbounded-buffer` places no practical limit on the size of the buffer
* `bounded-buffer` assumes that a fixed buffer size

Three things to take care of:
* When the `buffer` is full, no producer process will produce anything.
* When the `buffer` is empty, no consumer process will consume the data.
* No producer or consumer problem should work simultaneous on bounded buffer because buffer size is limited so we have to establish a synchronization between producer and consumer.

We can implement producer-consumer buffer with the `linear` or `circular` queue, with the position of `front` as _producer_ and `rear` as _consumer_.

Two ways to solve:

* **Simple Queue(Shared data):**

```c
# define BUFFER SIZE 8

int buffer [BUFFER SIZE];

int in = 0;  // in points to next free positon
int out = 0;  // out points to first full postion
int Counter = 0;
```
	Counter is incremented every time we(producer) add a new item(data) to the buffer & decremented every time we(consumer) remove one item from the buffer.

**producer problem**
```c
while (TRUE) {
	/* produce an item is nextProduced*/
	produce.item(nextProduced);
	while (counter == BUFFER_SIZE);
	/* do nothing */
	buffer[in] = nextProduced;
	in = (in + 1) % BUFFER_SIZE;
	counter ++;
}
```

**in** is address for empty slot in buffer.

Let's see how **counter++** works in assembly mode.
- load _counter's value_ into a register(let's say _Rp_)
- Increament _Rp_
- load _Rp_ register's value to counter

**Consumer problem**
```c
while (True) {
	while (counter == 0); /* do nothing */
	nextConsumed = buffer[out];
	out = (out + 1) % BUFFER_SIZE;
	counter --;
	/* consume the item in nextConsumed */
	process.item(nextConsumed);
}
```

**out** is address for filled slot in buffer.

Here, also counter will work in same as shown above.

We can see that this problem occurs in co-operative process, because both the program share same buffer(stack)
and **counter** variable.

Let's take a scenerio, where a buffer is half filled and process is going to increment counter but an interruption occurs, then context switching happens and control goes to consumer but when counter is decrementing again context switching occurs, like following flow:

> Producer > I1, I2 > Consumer > I1, I2 > Process > I3 > Consumer > I3

Now, according to this flow, we get value of **counter** 5 with producer, and value of **counter** is 3 by consumer. While, we have 4 in buffer. This leads to **Race Condition**. This happens vice-versa.

So, this is the **Producer Consumer Problem**.

# Printer Spooler Problem
This is a standard problem of process synchronization.
In this, we have a printer in network and several users to access that printer.
Printer is very slow peripheral device.

Spooler is like a program which stores all the data to print in a stack(or other) in sequential manner and then passes one by one.

If process wants to put docuement in spooler directory, then it has to execute these 4 lines of code:
```
* Load Ri, m[in]	// in tells position of empty slots, Ri is register with _i_ index
* Store SD[Ri], "File-name"		// SD -> spooler directory
* Increament Ri
* Store m[in], Ri
```

In case, if two or more co-operative process came to put docuemtn in spooler directory, they'll share _in_ variable.
This also have problem of process synchronization.

Let's say there are two co-operative process _P1 and p2_ to store file _F1.doc and F2.doc_. At current the value of _in_ is 0. Now, take this flow:

> P1 > I1, I2, I3 > P2 > I1, I2, I3, I4 > P1 > I4

Now, _in_ will point to 1, which is correct. But P2 will overwrite the file which P1 had stored before in 0 index.
Above scenerio can happen vice-versa also. It is one of the cases.

So, because of no synchronization, a process lost it's data.

# Semaphore
This is a method to prevent **Race Condition**, which mostly happens in co-operative processes(can happen in others also) which leads to _loss of data, deadlock etc._

**Semaphore** is an integer variable which is used in mutual exclusive manner by various concurrent co-operative process in order to achieve synchronization.

Semaphore is used basically in two types:
* Counting
* Binary

Now, we know _critical section_ contains common code for multiple co-operative processes.
But before accessing _CS_, process have to get through _entry code_ and after _CS_ then _exit section_. After that, process could be terminated.
There are various operations for _entry_ and _exit_ code. They are:

* P(), Down, Wait		// these are synonyms of one another
* V(), Up, Signal, Post, Release

They are are corresponding like P() -> V(), Down -> Up, Wait -> Signal/Post/Release

Now, presently critical section is free. So, to get entry in _CS_ process(P1) has to go to entry section, whose pseudo code is:
```c
Down(Semaphore S) {		// S in semaphore integer
	S value = S value - 1;
	if (S value < 0) {
	Put process (PCB) in suspended list, Sleep();
	}
	else
		return ;
}
```
If a process gets to critical section, it is considered successful, if it goes to block state, then it is considered unscuccessful.

If process has executed entry section and then _CS_, then it has to go through _exit section_, pseudo code is:
```c
Up(Semaphore S) {
	S value = S value + 1;
	if (S value <= 0) {
		Select a process from suspended list, WakeUp();
	}
}
```


So, basically above metioned method is approach by semaphore to avoid critical section problem.

## Counting Semaphore
In counting semaphore, _integer variable_ varies in between **-ve infinity** to **+ve infinity**. It isn't used much because we can do almost all the things which this can do with _binary semaphore_ which only contains 0 and 1.

## Binary Semaphore
In binary semaphore, _integer variable_ varies in between **0 and 1** only. Semaphore integers S is initialized with 1. On performing `down()` if we get S = 0, then this operation is considered successful and there's a chance of process to go to critical section. If S = 0 already then it can't perform _down()_ operation anymore because there are only two _0 and 1_. Operation will be considered unscuccessful. Process is blocked and goes to suspended list.

Here's the pseudo code
```c
// entry section
Down(Semaphore S) {
	if (S value == 1) {
		S value = 0;
	}
	else {
		block this process;
		place it in suspended list;
		sleep();
	}
}

// exit section
up(Semaphore S) {
	if (suspend list is empty) {
		S value = 1;
	}
	else {
		select a process from suspend list;
		wake-up();
	}
}
```

# Solving Producer Consumer Problem by using Semaphore

We will remove the problem of synchronization which we faced in _Producer Consumer Problem_. Following variables have been taken:

| Semaphore | Variable  | Description         |
| :---:     | :---:     | :---:               |
| Counting  | full = 0  | No. of filled slots |
|           | empty = N | No. of empty slots  |
| Binary    | S = 1     |                     |

Let's say there are two process _P1 and P2_.
So, to get through entry section and to get entry in critical section, producer have to go through following piece of code:
```c
produce_item(itemp);
down(s);
	buffer[in] = itemp;
	in = (in + 1) % N;
up(s);
up(full);
```
and consumer have to go through following:
```c
down(full);
down(s);
	itemC = buffer[out];
	out = (out + 1) % N;
up(s);
up(empty);
```

Conventions are as follow:

| **funtion/variable Notation** | **Use**                          |
| :---:                         | :---:                            |
| up()                          | code for entry section           |
| down()                        | code for exit section            |
| S                             | here denoting binary semaphore   |
|                               | integer variable                 |
| empty                         | count of empty slots in buffer   |
| full                          | count of full slots in buffer    |
| in                            | next empty slot to put data on   |
| out                           | points to first full slot to get |
|                               | data out                         |


Below is the image to solve producer consumer probelm using _semaphore_.
![producer consumer problem solution using semaphore(no-preemption)](images/OS_basics_img/pcps1.jpg)

The above method contains no context swtiching.
It's basically a no-preemption method and it's doing synchronization perfectly.
This can be happened vice-versa, means consumer can come first and producer later.

Now, we'll see solution for concurrent(co-operative) process. Synchronization in those types of process is difficult.

![producer consumer problem using semaphore(preemption)](images/OS_basics_img/pcps2.jpg)

We are trying to achieve serialization(synchronization) in concurrent processes.
That was solution of Producer Consumer problem with the use of Semaphore.

# Reader Writer Problem

In our database(computer system), we have two types of user operation perform:
* Reading
* Writing

Let's take an example:
```sh
$ cat file_operations/sample.txt	# this is read operation
  This is sample.txt
  It\'s running on hp 245 g5
  It is a simple text file
  It\'s kernel is Linux
  It\'s OS is manjaro with i3 as WM.
$
$ echo "$USER" >> file_operations/sample.txt	# this is write operation
  This is sample.txt
  It\'s running on hp 245 g5
  It is a simple text file
  It\'s kernel is Linux
  It\'s OS is manjaro with i3 as WM.
  raytracer
```

Multiple users(reader or writer) can use data. But there's a catch, if a _reader_ is reading a data
and a _writer_ comes in same data then this is problem.
So, problems on same data can be seen like:
```
R - W => problem	# R = Reader, W = Writer
W - R => problem
W - W => problem
R - R => no problem
```

These are standard problems of **DBMS**.
We'll use _binary semaphore_ to deal with these three problems. Semaphore because we want to synchronize reader and writer process.

Here is the pseudo code, _reader_ and _writer_ have to execute before getting inside critical section.
```c
// global variables
int rc = 0;		// read count
semaphore mutex = 1		// binary semaphore variable
semaphore db = 1		// binary semaphore variable

// reader's code for entry section
void Reader(void) {
	while(TRUE) {
		// semaphore operation
		down(mutex);
		rc = rc + 1;
		if (rc == 1) {
			down(db);
		}
		up(mutex);

DATABASE	// database, critical section

		// reader's code for exit section
		down(mutex);
		rc = rc - 1;
		if (rc == 0) {
			up(db);
		}
		up(mutex);
		Process data	// do data processing
	}
}

// writer's code for entry and exit section
void Writer(void) {
	while(TRUE) {
		down(db);
		DATABASE	// critical section
		up(db);
	}
}
// I can't understand the use of mutex, code should work fine without it
```
In the above mentioned, `db` is an important _variable_ as it controls the reader and writer if any one of them is inside critical section. Also, in the above solution `R - R`(multiple reader's) can get entry to critical section as it isn't a problem. So, we have achieved synchronization and got a way to deal with **Reader Writer Problem.**

# Dining Philosopher Problem

Like others, **Dining Philosopher Problem** is also a standard problem of Operating System.

## What's this problem?
In this problem, there are 5 `philosophers` around a `dining table` and they all have `fork/spoon/chopstick`(taking fork here). In the center, of table there is a bowl containg `noodle/rice/sphagetti`(taking rice here).

![dining philosopher problem](images/OS_basics_img/din_philos.jpg)

Now, every philosopher is having two states:
* think
* eat

So, they think and then after eat(depends on scenerio, but that's the two thing they'll do). But they have to follow a set of rule in this process. Let's consider there are five philosophers `F0, F1, F2, F3, F4` and so five forks `F0, F1, F2, F3, F4`.
> here 0, 1, 2... are index numbers

These set of rules are given here as pseudo code
```c
void Philosopher(void) {
	while(TRUE) {
		Thinking();
		take-fork(i);	// i is index number for philosopher
		// consider i as left fork of philosopher
		take-fork((i + 1) % N)	// N is total number of philosopher/fork
		// consider this as right fork of philosopher
		EAT();
		put-fork(i);
		put-fork((i + 1) % N);
	}
}
```

### case 1:
Now, according to this code, if philosophers goes one by one to eat then there is no problem. It can be considered as **case 1**.

### case 2:
Let, `P0` after `thinking()` takes fork `F0`, but before taking _right fork_ `F1` process `P1` comes in and takes fork `F1`. Now, till `P1` hadn't put down it's left fork(), `P0` can't eat as it's his right fork.

This condition could go on. Maybe before _P1_ takes right fork _F2_, P2 comes in and takes it left fork _F2_. So, this leads to **Race Condition**.

To avoid this, we will be using _binary semaphore_. We will take an _array_ of semaphore binary integers **S[i]**. Total size of array will be of total count of _philosophers_. All the integers, (here, total 5) _S[0] S[1] S[2] S[3] S[4]_ will be initialized with value **1**.

So, the above pseudo code will be changed like this
```c
void Philosopher(void) {
	while(TRUE) {
		Thinking();
		// entry section
		wait(take-fork(S[i]));	// down operation semaphore
		wait(take-fork(S[(i + 1) % n]));
		// critical section
		EAT();
		// exit section
		signal(put-fork(i));	// up operation semaphore
		signal(put-fork((i + 1) % N));
	}
}
```
Now, we know in binary semaphore we take `1` as _green flag_ and `0` as red flag to process to go on. So, according to above code, if any _S[i]_(generally, it will be the left fork of philosopher) is `0` and since it's binary semaphore integer(value can't be down than 0) then that `philosopher` can't go through `entry section`.

For better understanding, see the table below to see which philosopher is related with which semaphore integer

> Rule is: S[i] and S[(i + 1) % N]

| P0  | S0  | S1  |
| --- | --- | --- |
| P1  | S1  | S2  |
| P2  | S2  | S3  |
| P3  | S3  | S4  |
| P4  | S4  | S0  |

Now, in mutual exclusion we know that only process can be in _critical section_ at a time. But see this, if `P0` goes in then `P1` can't(S1 will already be 0). Now, if `P2` tries to go in, it can easily go to _CS_. So, we saw that in _dining philosopher_, this is a special case that

`More than a process(philosopher) can enter the critical section but they should be independent(fork/semaphore variable)`.

On the above scenario, `P0` and `P2` are independent, they don't share any common semaphore variable(see table above).

### case 3:

Let's see if deadlock occurs in this problem or not. Visualize the scenario given down:
> Note: relate forks (like F1, F2, ...) to semaphore integers (like S1, S2, ...)

So, consider the follwing scenario. All the semaphore integers are `1` currently means, no philosopher has taken any fork. Now,
* Philosopher 0 comes and takes F0 but is preempted(any reason may be time-quantum or low priority) so can't take F1
* Philosopher 1 and takes it's left fork F1(since it isn't taken by P0 right now) but gets preempted
* P2 comes and takes it's left fork and gets preempted
* P3 comes, takes it's left fork and gets preempted
* P4 comes, takes it's left fork and gets preempted

So, all the process has executed `wait(take-fork(S[i]))` but not `wait(take-fork(S[(i + 1) % n]))`. Now,

* P0 requires right fork F1(S1) but can't. P1 have F1(S1 is already 0).
* P1 requires F2 but can't. P2 have F2.
* P2 requires F3 but cant't. P3 have F3.
* P3 requires F4 but can't. P4 have F4.
* P4 requires F0 but can't. P0 have F0.

So, as you can see here, all the process have a resource and they requires another resource which they can't get(all S[i] is 0 currently). Now, this can't be undone since no S[i] can be done to 1.

So, this is a state of **Deadlock.**

The best solution, we get to avoid this _deadlock_ is if way change the sequence of `take-fork();` for `last philosopher`. Means the table will look something like this


| P0  | S0  | S1  |
| --- | --- | --- |
| P1  | S1  | S2  |
| P2  | S2  | S3  |
| P3  | S3  | S4  |
| P4  | S0  | S4  |

So, now let's start from begining. All the `S[i]` are `1` currently. Every philosopher, starts to take it's left fork _one-by-one_. So, now visualize this scenerio again

* Philosopher 0 comes and takes F0 but is preempted(any reason may be time-quantum or low priority) so can't take F1
* Philosopher 1 and takes it's left fork F1(since it isn't taken by P0 right now) but gets preempted
* P2 comes and takes it's left fork and gets preempted
* P3 comes, takes it's left fork and gets preempted
* P4 comes and now it tries to take it's right fork(not left) but since it's already takeing by _P0(S0 in already 0)_ P4 will be blocked.

In this way, we can avoid deadlock, now
_P3 can take F4 and eat and after it'll put F3 and F4(put-fork S[i] and put-fork S[(i + 1) % n]). So after P3, both P2 and P4 can eat(if needed) since both are independent._

It's not mendatory, to reverse the sequence of last philosopher. We can reverse the sequence for any one of the philosopher.

So, this was Dining Philosopher Problem.

# Monitor vs. Semaphore

Semaphore and Monitor both allow process to acces the shared resources in mutual exclusion. Both are the process synchronization tool. Instead, they are very different. **Semaphore** is an integer variable which can be operated only by _wait()_ and _signal()_ operation apart from the initialization.

**Monitor** on the other hand is an abstract data type whose construct allow one process to get activate at one time.

## Compariosion table

| Basis For Compariosion | Semaphore | Monitor |
| :---: | :---: | :---: |
| Basic | Semaphore is an integer variable S | Monitor is an abstract data type. |
| Action | Value of Semaphore S indicates | The Monitor type contains |
| | the number of shared resources | shared variables and the set of |
| | available in the system | procedures that operate on the |
| | | shared variable. |
| Access | When any process access the | When any process wants to |
| | shared resource it perform wait() | wants to access the shared |
| | operation on S and when it releases | variables in the monitor, it needs |
| | the shared resource it performs | to access it through the |
| | signal() operation on S | procedures |
| Condition Variable | Semaphore does not have | Monitor has condition |
| | condition variable | variables |

### Semaphore

Semaphore integer variable S is initialized to the **number of resources** present in the system. The value of semaphore S can be modified only by two functions **wait()** and **signal()** apart from initialization.

In this, no multiple process can simultaneously modify the value of the semaphore. Further, the operating system distinguishes the semaphore in two categories, **Counting** and **Binary** Semaphore.

In **Counting Semaphore**, the value of semaphore S is initialized to the number of resources present in the system. Whenever a process wants to access the shared resources, it performs **wait()** operation on the semaphore which decrements the value of semaphore by one. When it releases the shared resource, it performs a **signal()** operation on the semaphore which increments the value of semaphore by one.

When the semaphore count goes to **0**, it means all resources are occupied by the processes. If a process need to use a resource when semaphore count is 0, it executes wait() and get **blocked** until a process utilizing the shared resources releases it and the value of semaphore becomes greater than 0.

In **Binary semaphore**, the value of semaphore ranges between 0 and 1. It is similar to mutex lock, but mutex is a locking mechanism whereas, the semaphore is a signaling mechanism. In binary semaphore, if a process wants to access the resource it performs wait() operation on the semaphore and decrements the value of semaphore from 1 to 0.

When process releases the resource, it performs a signal() operation on the semaphore and increments its value to 1. If the value of the semaphore is 0 and a process want to access the resource it performs wait() operation and block itself till the current process utilizing the resources releases the resource.

### Monitor
To overcome the timing errors that occurs while using semaphore for process synchronization, the researchers have introduced a high-level synchronization construct i.e. the **monitor** type. A monitor type is an **abstract data type** that is used for process synchronization.

Being an abstract data type monitor type contains the shared data variables that are to be shared by all the processes and some programmer-defined operations that allow processes to execute in mutual exclusion within the monitor. A process can not directly access the shared data variable in the monitor; the process has to access it through procedures defined in the monitor which allow only one process to access the shared variables in a monitor at a time.

Monitor's syntax is as follow:
```c
monitor monitor_name {
	// shared variable declarations
	procedure P1 ( . . . ) {
	}
	procedure P2 ( . . . ) {
	}
	procedure Pn ( . . . ) {
	}
	initialization code ( . . . ) {
	}
}
```
A monitor is a construct such as only one process is active at a time within the monitor. If other process tries to access the shared variable in monitor, it gets blocked and is lined up in the queue to get the access to shared data when previously accessing process releases it.

Conditional variables were introduced for additional synchronization mechanism. The conditional variable **allows a process to wait inside the monitor** and allows a waiting process to resume immediately when the other process releases the resources.

The conditional variable can invoke only two operation wait() and signal(). Where if a process P invokes a wait() operation it gets suspended in the monitor till other process **Q invoke signal()** operation i.e. a signal() operation invoked by a process resumes the suspended process.

# Sleeping Barber problem

In this problem, you can think of barber as _processor_ to do processing and customer as _process_. This is a semaphore problem. Now, think of it like this:

If there is no customer(process), barber(processor) goes to sleep. If barber chair is already occupied means if a process is already in critical section and waiting queue is full then customer leaves the shop or process gets in block state. If waiting queue is free(even one chair only) and if a process is already in critical section, newly arrived customer(process) sits to that chair. And at last, if barber is asleep(means processor has nothing to process) and a new customer(process) arrives then it wakes the barber.

In this problem, we can see _mutual exclusion, progress_ which are the basic properties for process synchronization and problem for process synchronization can be solved using semaphores. So, here's the pseudo code
```c
#define CHAIR N		// put a value(large) in place of N
typedef int semaphore;
semaphore customers = 0;	// no. of waiting customers for service
semaphore barbers = 0;		// barbers not ready(sleeping)
semaphore mutex = 1;		// for mutual exclusion
int waiting = 0;			// customers waiting

void barber(void) {
	while (TRUE) {
		down(&customers);	// go to sleep, if no customers
		down(&mutex);		// get access of waiting
		waiting = waiting - 1;	// decrement count for waiting customers
		up(&barbers);		//  a barber is ready now for service
		up(&mutex);			// release 'waiting'
		cut_hair();			// gets through critical section
	}
}

void customer(void) {
	down(&mutex);		// enter critical section
	if (waiting < CHAIR) {		// if no free chair, then else
		waiting = waiting + 1;	// increment count of waiting process
		up(&customer);		// wake barber if necessary
		up(&mutex);			// release access to 'waiting'
		down(&barbers);		// go to sleep, if no. of barbers is 0
		get_haircut();		// processing
	}
	else {
		up(&mutex);			// shop is full, leave
	}
}
```

# Memory Management

Managing all the memory resources in more efficient manner in a computer system comes under the section of **Memory Management** in OS.

Majority of concentration is on **Primary Memory** since, CPU is directly connected with Registers, Cache and RAM.
We load the programs from secondary memory to primary for execution. So, they load in RAM. Cache and Registers can also be used but they are much smaller in size than _RAM_. We'll be talking about RAM here.

Let, only one process could load in _RAM_. Now, if probability of process performing _I/O operation_ is **K** then, `cpu-utilization` will be **(1-K)**.
If two process are loaded then probability will be **K*K** and `cpu-utilization` will be **(1-k*k)**.

So, to increase `degree of multi-programming`, we have to increase size of RAM, so that more processes could be accomodated and thus `cpu-utilization` will be increased.

So, there's concept for `allocation-deallocation` and for the `data-security` like data should not be breached while transfer of data. So, memory management is to bring more and more process to **primary memory**. Also, there will be segmenting(bringing only piece/block) of process to bring inside primary memory.

> Note: Onwards, I'll use memory for primary memory.

## Memory Management Techniques:

We can have more and more process inside memory if we have higher degree of multiprogramming.
If we have more process in ready state then cpu-utilization will be higher.

Here are the memory management techniques:
* Contiguous
	+ Fixed Partition/Static
	+ Variable Partition/Dynamic
* Non-contiguous
	+ Paging
	+ Multi-level Paging
	+ Inverted Paging
	+ Segmentation
	+ Segmented Paging

### Contiguous Memory Allocation:
It means that we(OS) tries to give continuous memory addresses to same type processes or to all the blocks of processes like that.
In it, we have `fixed` type which means OS divides processes to blocks according to some filter(maybe size etc.) to all the process.
In `dynamic` type, process are divided on run-time dynamically according to the need.

### Non-continuous Memory Allocation:
In it, let's first block is partition of process P1, second block of memory is of process P2, third block is second partition of P1, forth is fist partition of P4. These blocks can also be blank.

So, overall, if same types of process or the partitions of a process aren't necessarily given continuous memory address in this type of memory allocation.

## Fixed Partitioning

Fixed partition, also known as `static partition` have following properties:
- Number of partitions are fixed.
- Size of each partition may or may not be same.
- Contiguous allocation so spanning(splitting process and putting it in different blocks which can be contiguous) is not allowed.
- We can start putting an incoming process at any block partition, only condition is that size of process should be less than size of block partition.
- But we try to allocate from _0_ index.
- There's also a con of this, let's size of smallest block partition is `4mb`.
So, if a process of `2mb` is loaded on that block partition then rest of the `2mb` of that block is waste.
- This is called `Internal Fragmentation`. We can't allocate this left memory.
- In case, if a process comes whose size is less than size of total block process combines but greater than every block process individually. So, that process can't be loaded.
- So, this is con as limit in process size.
- Also, we can't put process more than total number of block partitions.
- So, this is `limitation in degree of multiprogramming`.
- The same problem will occur even if size of every individual block partition is same or different.
- When every block partition is filled with process. Now, if new process comes and it's size is less than total combined size of left,
like size of process is **5mb** and total left memory size from every block partition as of _integer fragmentation_ is **6mb**.
So, still we can't put that process. This is known as `External Fragmentation`.
- A pro is, allocation/de-allocation in this method is easy.

## Variable Partitioning

In this we don't partition the memory blocks before. Partitioning is done in runtime according to the need and size of process.

* There's no chance of `internal fragmentation` in this.
* `Degree of multiprogramming` is increased very much in compared to **fixed partitioning** method.
* No limitation on `number of process`.
* No limitation on `process size` also.
* When process leaves the block, it creates a hole.
* Let's say, partition[2] process leaves the blocks and frees **4mb** and partition[4] and also frees **4mb**.
Now let's say a process comes of **8mb** size but we can't allocate this process in memory.
This is known as `External Fragmentation.`
* We can resolve external fragmentation with `Compaction.` Means all the allocated process is moved to a single place and free blocks are put together.
* It's con is that, we have to put running process to _halt_ and it'll take a lot of time.
* `Allocation & De-allocation` is a bit complex than compared to _fixed partitioning_.
Because, holes and process are created and allocated dynamically.

> External Fragmentation is more serious to Internal Fragmentation

## Thrashing

Thrashing is directly linked to `Degree of Multiprogramming`.
To increase `cpu utilization`, we try to bring maximum process to RAM.
But since, RAM size is limited, we use concept of `paging.`

In `paging`, we divide processes to multiple _pages_ and these pages are then called into RAM.

So, now we try put single or few pages of every process(as many as possible) into RAM.
In this way, every process is involved. This has advantage but also disadvantage.

Disadvantage is the whole process isn't present, let's say if CPU demands a page of process which isn't loaded in RAM,
and this happens for all the process's pages present in RAM then this leads to `Page Fault.`

Whenever, _page fault_ happens, we use `Page Fault Service time` which means we bring the demanded page from hard-disk which takes a lot of time.
So, OS will get busy in sevice of page fault.

![Thrashing Graph](images/OS_basics_img/thrashing_graph.png)

According to the graph above, at a particular time of `degree of multiprogramming` our `CPU utilization` will be high. Let's say that point is `lambda`.

After that, `thrashing` will start instantly means `Page hit` decreased and `Page Fault` increased.
At this time, `CPU Utilization` is decreased way too much now.

A fix for this can be to increase the size of **Main Memory**.
But it isn't possible always.
Another way is, if we tell `LTS(long term scheduler)` to slow it's rate or to don't take _degree of multiprogramming_ at **threshold** level. In this way, we can prevent `Thrashing`.

## Memory Allocation Techniques

* OS use 3 strategies in order to allocate the process in the free partition or holes.
* These 3 strategies are used in fixed size as well as variable size partition
and uses it to place the process from _secondary_ to _primary_ memory.
* 3 strategies are:
    - **First Fit:** Allocate the first hole that is big enough
    - **Best Fit:** Allocate the smallest hole that is big enough
    - **Worst Fit:** Allocate the largest hole
* The performance of these 3 strategies will differ in fixed and variable size partitioning.

## Memory Management Unit (MMU)

* Hardware device that at runtime maps virtual address to physical address is/are **MMU**.
* Base register are now called `relocation register`.
* Translation of _logical_ to _physical register_ is done by
    local address + relocation register(MMU) = physical address


## Non-Contiguous Allocation

Partitioning a program into a number of small units, each of which can reside in a different part of the memory.

Two types:
* Paging
* Segmentation

## Paging

* Physical address space of a process can be non-contiguous.
* Process is divided into fixed-size blocks, each of which may reside in a different part of physical memory.
* _Divided physical memory into fixed-sized blocks called_ `frames`.
* _Divide logical memory(process) into blocks of same size as frames called_ `pages`.
* It means process is divides to _pages_ and that page is mapped to _frame_.
* _Backing store(dedicated disk), where the  program is permanently residing,
is also split into storage units(called_ `blocks or pages` _)which are the same size as the frame and pages_.
* Paging:-
    - Avoids external fragmentation
    - Still have Internal fragmentation(because of fixed size partition)

> Pages are in Secondary Memory. Means when LTS is transferring a process to main memory it manages a table which is called Page Table.

```
    Process -> divided in pages(same size as of frame) -> Mapping -> Frame(Main Memory/Collection of frames) -> CPU
```

### Address Translation Scheme

* Assume the logical address is 2^m.
* Assume page size is 2^n.
* Address generated by CPU is _divided_ into:
    - **Page number(p) -** used as an index into a _page table_ which contains base address
of each page in physical memory.
Size of **p** is `m - n`
    - **Page offset(d) -** combined with base address to define the physical memory address
that is sent to memory unit. Size of **d** is `n`.

Page table contains of two entries:-
* Page number
* Frame number

It means that which page number(from sec. memory) is mapped to which page number(from pri. memory).


To accept a address from any physical address, we have to first access _page table_
which is also showed in physical memory and then again to physical memory to search for related frame.

```
P.A. = (Frame No. * Page Size + Offset)

P.A. = Physical Address
L.A. = Logical Address
```

> Also checkout TLB(Translation Lookaside Buffer)

### Memory Protection
* Memory protection implemented by associating protection bits with each frame
to indicate "read-only" or "read-write" access is allowed.
    - Can also add more bits to indicate "execute-only" and so on
* `Valid-Invalid` bit attached to each entry in the page table:
    - "valid" indicates that the associated page is in the process's logical address space,
and is thus is a legal page
    - "invalid" indicates that the page is not in the process's logical address
> So add third column now to Page Table of Valid-Invalid bit
* Any violations results in a trap to the kernel.

