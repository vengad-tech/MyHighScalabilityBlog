---
title: "Introduction to Asynchronous I/O"
date: 2018-08-25T17:35:00+05:30
archives: "2018"
tags: []
categories:
- AsyncIO
author: Vengadanathan
---

## What is Asynchronous I/O ?

Input and output i.e I/O operations on a computer can be very slow compared to the processing of data i.e CPU intensive work. An I/O device can incorporate mechanical devices that must physically move, such as a hard drive seeking a track to read or write; this is often orders of magnitude slower than the switching of electric current. For example, during a disk operation that takes ten milliseconds to perform, a processor that is clocked at one gigahertz could have performed ten million instruction-processing cycles.

Tradionally when we make any IO operations i.e writing to or reading from a disk, then the call to do so gets blocked (also blocking other processing calls) until it is done, this is called synchronous I/O.

Alternatively, it is possible to start the communication and then perform processing that does not require that the I/O be completed. This approach is called asynchronous input/output.

### Async I/O with Example

Let's say we need to execute the following two routines concurrently, represented as steps/instructions below. 
(__For simplicity’s sake let assume below they are executed by single thread one after the another__).

**Route 1:**

1. Generate random set of numbers and compute SUM of them
2. write SUM computed to disk
3. Send output to user

**Routine 2:**

1. Generate random set of numbers and compute PRODUCT of them
2. Make PRODUCT computed to disk
3. Send output to user

As you see above step 1 of both the routines are lightweight operations (which does not actually involves disk I/O).
If we follow synchronous I/O then step 2 of routine one will wait for disk operation to complete, then step 3 of routine one will be executed followed by step 1,2 and 3 of routine two.

So time taken to execute above routines in synchronous I/O is 

```
Total time taken = Routine one's step 1 + step 2 + step 3 execution time + Routine two's step 1 + step 2 + step 3 execution time
```

If we can model execution of above steps differently using asynchronous I/O without waiting for Step 2 write to complete.

when we reach routine one's Step 2, instead of waiting for the disk I/O to complete we move to next waiting routine (i.e when routine one is performing disk I/O it unblocks and provides way for other routines to execute), in this case routine two's Step 1 will be executed to generate random number and compute product out of it and when routine 1's step 2 is executed (which is again Disk I/O) instead of blocking for it, it provides way for Routine 1 to resume its execution i.e step 3 of routine 1 is executed and routine one gets completed, now routine two will be resumed (i.e it will wait until step 2 of routine two get completed) and step 3 will be executed.

Using Async IO approach the total time taken to execute would be

```
Total time taken = Routine one Step 1 execution time + Max(Routine one Step 2 execution time, Routine two Step 1 execution time) + max(Routine two Step 2 execution time, Routine one Step 3 execution) + Routine two step 3 execution
```

We could further simplify it by changing it to 

```
Total time taken = Routine one's Step 1 execution time +Routine one's Step 2 execution time + Routine two's Step 2 execution time + Routine two's Step 3 execution time
```

Above execution time is derived assuming Routine one's Step 2 execution time is will be greater than Routine two's Step 1 execution time and Routine two Step 2 execution time will be greater than Routine one Step 3 execution time (as they are expensive disk operations)

**Thus we saved overall executing time of two routines by using Async IO efficiently!**

If you are not quite getting it yet, We can visualize the execution of above set of instructions for better understanding:

**Execution flow in Synchronous I/O**

![Imgur](https://i.imgur.com/B6CGu9z.png)

**Execution flow in Asynchronous I/O**

![Imgur](https://i.imgur.com/MASgyhm.png)

## When to use Asyc I/O ?

As we learned from above, I/O calls won't be blocking instead we proceed with executing the instructions.
This allows multiple routines to co-ordinate and execute by interleaving their execution's when-ever blocking call happens.

Async I/O would he helpful only in cases where there are lots of I/O calls made i.e so that we can switch to another routine and execute it. If any of the routine makes very high CPU intenstive call then it is blocking call and all other processing routine needs to wait for it.

Ex: A webserver handling requests and performing I/O operation in response can benefit from using Async I/O where a single thread when handling request can switch context to handle additional incoming request's if it performs I/O operation as part of handling requests thus making efficient utilization of CPU cycles without blocking or waiting for any I/O activies.

## What's next ?

We will learn how Async IO is being implemented across various programming languages and low level concepts in next series.
