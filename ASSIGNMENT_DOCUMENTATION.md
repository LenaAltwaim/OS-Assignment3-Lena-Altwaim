# Assignment 3 - Complete Documentation

**Student Name**: [Lena abdullah altwaim]  
**Student ID**: [445052077]  
**Date Submitted**: [4th may]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: (https://drive.google.com/file/d/1ZGuCNZoy-t-EsBMyObSG1oEpV6bpLw3C/view?usp=sharing)

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [May 3, 8:30pm]
**What I implemented**: 
Set my student ID and reviewed the given code to understand shared resources and potential race conditions.
**Challenges encountered**: 
Understanding where race conditions could occur in the code.
**How I solved it**: 
Reviewed lecture notes and textbook explanation of shared memory access.
**Testing approach**: 
Ran the program to observe behavior without synchronization.
**Time spent**: 
30 mins
---

### Entry 2 - [may 3, 9:00]
**What I implemented**: 
Added ReentrantLock to protect shared counters (contextSwitchCount, completedProcessCount, totalWaitingTime).
**Challenges encountered**: 
Ensuring proper lock usage without forgetting unlock.
**How I solved it**: 
Used try-finally blocks to guarantee unlocking.
**Testing approach**: 
Ran program multiple times to ensure counters were consistent.
**Time spent**: 
30 mins
---

### Entry 3 - [may 3, 9:30]
**What I implemented**: 
Protected executionLog using ReentrantLock.
**Challenges encountered**: 
Understanding why ArrayList is not thread-safe.
**How I solved it**: 
Studied concurrency issues in collections.
**Testing approach**: 
Checked that no ConcurrentModificationException occurred
**Time spent**: 
20 mins
---

### Entry 4 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

 1st race condition occurs in the shared variable contextSwitchCount. This variable is incremented by multiple threads inside the run() method. Without synchronization, two threads may read the same value at the same time and both increment it, causing one update to be lost.

Code example (before synchronization):

public static void incrementContextSwitch() {
    contextSwitchCount++; // Not thread-safe
}

In this case, if two threads execute this line simultaneously, the final value may be incorrect due to lost updates.

2nd race condition occurs in the executionLog ArrayList. Multiple threads may attempt to add elements concurrently. Since ArrayList is not thread-safe, this may result in inconsistent data or runtime exceptions such as ConcurrentModificationException.

Code example (before synchronization):

public static void logExecution(String message) {
    executionLog.add(message); // Not thread-safe
}

If multiple threads call this method at the same time, the internal structure of the ArrayList may become corrupted.

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

A ReentrantLock is used for mutual exclusion, ensuring that only one thread can access a critical section at a time. It is suitable for protecting shared variables.

A Semaphore, on the other hand, controls access to a limited number of resources. It allows multiple threads to access a resource depending on the number of permits.

In my implementation, I used ReentrantLock to protect shared counters and the execution log. I used a Semaphore with one permit to simulate a single CPU, ensuring that only one process executes at a time

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

Deadlock is a situation where two or more threads are blocked forever, waiting for each other to release resources.

One prevention technique is using try-finally blocks to ensure that locks are always released, even if an exception occurs. Another technique is avoiding nested locks or ensuring a consistent lock ordering.

In my code, I used try-finally blocks to guarantee that locks and semaphores are always released, preventing deadlock situations.

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

I used a single lock (coarse-grained locking) to protect all three counters. This simplifies the design and reduces the complexity of managing multiple locks.

The advantage of this approach is that it is easier to implement and avoids potential deadlocks caused by multiple locks. However, it reduces concurrency because only one thread can update any counter at a time.

Fine-grained locking would allow better concurrency since each counter could be updated independently. However, it increases complexity and the risk of deadlocks.

Although the counters are independent, I chose coarse-grained locking for simplicity and safety in this assignment.

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount, completedProcessCount, totalWaitingTime
**Why they need protection**: 
Multiple threads update them simultaneously, leading to race conditions.
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```java
lock.lock();
try {
    contextSwitchCount++;
} finally {
    lock.unlock();
}
```

**Justification**: 
Ensures mutual exclusion and prevents lost updates.
---

### Critical Section #2: Execution Log

**What resource**: 
executionLog (ArrayList)
**Why it needs protection**: 
ArrayList is not thread-safe and concurrent modification may cause errors.
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```java
lock.lock();
try {
    executionLog.add(message);
} finally {
    lock.unlock();
}
```

**Justification**: 
Prevents concurrent modification issues and ensures data consistency
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
Control CPU access
**Number of permits and why**: 
1 permit to simulate a single CPU
**Where implemented**: 
In run() method before execution
**Code snippet**:
```java
SharedResources.cpuSemaphore.acquire();
try {
    // execution
} finally {
    SharedResources.cpuSemaphore.release();
}
```

**Effect on program behavior**: 
Ensures only one process executes at a time, preventing conflicts
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results
I tested whether the program produces consistent and correct results when executed multiple times.
**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)
# Compile the program
javac SchedulerSimulationSync.java

# Run the program multiple times
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
```

**Results**: 
(Show that running multiple times produces consistent, correct results)
The program produced consistent results in all runs.
The number of completed processes always matched the total number of processes.
The context switch count remained logical and consistent.
No unexpected behavior or crashes occurred.
**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)
Without synchronization, race conditions could occur when multiple threads update shared variables such as:
contextSwitchCount
completedProcessCount
totalWaitingTime
executionLog
This could lead to:
Incorrect counter values
Missing log entries
Data inconsistency
Even if errors do not appear every time, they are still possible due to unpredictable thread scheduling.

**Conclusion**: 
Synchronization ensures consistent and reliable program behavior across multiple executions.
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException
I tested whether the program throws a ConcurrentModificationException when multiple threads access the execution log.
**Testing procedure**: 
Ran the program multiple times with synchronization enabled
Observed the execution log behavior
Compared with expected issues from unsynchronized ArrayList
**Results**: 
No ConcurrentModificationException occurred during execution.
The execution log was updated correctly without corruption.
**What this proves**: 
This proves that the execution log is properly protected using ReentrantLock, ensuring safe concurrent access.
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)
I verified the correctness of final statistics such as:
Total completed processes
Total waiting time
Context switch count
**Expected values**: 
Completed processes = total number of processes generated
Waiting time should be non-negative
Context switches should reflect scheduling behavior
**Actual values**: 
From the output:
Total Completed Processes = 13
Total Context Switches = 29
Total Waiting Time = 863779 ms
**Analysis**: 
The actual values matched expectations.
All processes completed successfully
No missing or duplicated processes
Statistics were logically consistent with program behavior
---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]
I tested the program with different randomly generated process values (burst time and priorities) by changing the student ID seed.
**Purpose**: 
To verify that synchronization works correctly under different scheduling conditions.
**Results**: 
The program handled all scenarios correctly
No crashes or inconsistencies occurred
Output remained stable and logical
**What I learned**: 
Synchronization mechanisms must work correctly under all conditions, not just one specific scenario. Proper use of locks and semaphores ensures robustness.
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]
Synchronization is essential in multithreaded systems to ensure correct and predictable behavior when multiple threads access shared resources. I learned how race conditions occur when threads access shared variables without proper coordination, leading to inconsistent results. Using ReentrantLock helped me understand how mutual exclusion works to protect critical sections. I also learned how semaphores can be used to control access to limited resources such as a CPU.One important lesson was the necessity of using try-finally blocks to ensure locks are always released, preventing deadlocks. Additionally, I understood the difference between protecting data (locks) and controlling access (semaphores). Overall, this assignment improved my understanding of concurrency control and its importance in operating systems.
---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
In banking systems, synchronization is required when multiple users access the same account. Without proper locking, concurrent withdrawals could lead to incorrect balances.
**Example 2**: 
In operating systems, CPU scheduling requires synchronization to ensure that only a limited number of processes access the CPU at a time, similar to how the semaphore was used in this assignment.
---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]
Synchronization can be explained as a way to organize access to shared resources so that multiple threads do not interfere with each other. For example, imagine a single bathroom shared by many people. Only one person can use it at a time, so a lock is used to ensure exclusive access. Similarly, in programming, locks ensure that only one thread modifies shared data at a time. Semaphores are like allowing a limited number of people into a room at once. This concept helps prevent errors and ensures that programs behave correctly even when multiple threads run concurrently.
---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/LenaAltwaim/OS-Assignment3-Lena-Altwaim.git

**Number of commits**: 10

**Commit messages**: 
1. student id changed : 445052077
2. Lock and Semaphore Added for synchronization and import packages
3. Protect shared counter ( contextSwitchCount++) using ReentantLock named lock and release
4. protect the shared virable (completedProcessCount) using ReetantLock lock and finally release the lock to prevent deadlock
5. protect shared variable totalWaitingTime using Reentlock
6. protecting executionLog
7. Use semaphore to control CPU acsess in process execution and in the finally block its released
8. Apply semaphore in runTo Completin method
9. Answering assignment doc
10. finishing Assignment doc

---

## Summary

**Total time spent on assignment**: 
7-8 hours
**Key takeaways**: 
1. Synchronization is necessary to prevent race conditions in multithreaded systems.
2. Locks provide mutual exclusion, while semaphores control access to resources.
3. Proper use of try-finally blocks prevents deadlocks and ensures system stability.

**Most challenging aspect**: 
Understanding how race conditions occur and identifying all critical sections in the code.
**What I'm most proud of**: 
Successfully implementing synchronization mechanisms and ensuring the program runs correctly and consistently without errors.
---

**End of Documentation**
