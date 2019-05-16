# User-Level Thread Library by Grayson Gordon and Kat Arnott

## Phase 1: Semaphore API
  Our semaphore struct was created to include two member variables, one for the count, and one associated queue of tasks waiting for a semaphore resource. Appropriately, our ```sem_up``` and ```sem_down``` functions appropriately increment or decrement the 'count' of available resources for a given semaphore, and will either enqueue and block a thread, or dequeue and process a thread (if waiting and next in line).
  
  However, there comes an issue where a thread (Thread A) is asleep, waiting for some resource, and it is called to wake and access the next resource once it becomes available, but this resource is taken by some other thread first (Thread B). In order to handle this scenario, we created a critical section in ```sem_down```, such that a thread would be pushed onto the semaphore queue when there were no resources available and be blocked. Whenever a thead in this critical section awakens, it will recheck if there is a resource available before taking it. In this way, if some Thread B takes the resource, Thread A will go back to sleep and both will not try to proceed with insufficient resources.

## Phase 2: TPS API
The goal of the TPS API is to enable private storage for each thread in a process, such that no thread can read from or write to the memory table of another thread.

### Phase 2.1: Unprotected TPS with Naive Cloning
In this step, we created the TPS struct, which contains the tid of the thread using it, and the address of the associated memory page. Upon creation, the TPS were unprotected in that they had no read or write protections. When cloning, the entire contents of a TPS were simply copied over to the new thread's memory page.

### Phase 2.2: Protected TPS
In this step, followed the prompt to protect the read and write of a new TPS memory page by changing permissions. We modified the provided ```segv_handler``` function to ensure appropriate differentiation between TPS Protection Faults and actual segfaults.

### Phase 2.3: Copy-on-Write Cloning
In this step, we added a PAGE struct, which contains the number of TPS's referencing it, as well as the address of the associated memory page contents. The TPS struct was modified to contain a pointer to the PAGE struct that the thread was referencing. In this way, multiple threads were capable of referencing the same memory page. We modified ```tps_clone()``` so that when a new TPS was cloned, the clone points to the same memory page of the TPS that requested a clone. We updated ```tps_write()``` such that if multiple threads were referring to the same page in memory, the page would be cloned and the clone would be written to.


## Testing:
Tester Files Provided for P3:

-```sem_buffer.c```

-```sem_count.c```

-```sem_prime.c```

-```tps.c```

Additional Tester Files Generated:

-```tps_testsuite.c```

In addition to the files provided, we implemented a test file which asserts that no threads are reading or writing outside of their own memory pages and that a TPS Protection Segfault is thrown if this occurs. This is done by iterating through all the TPS areas and searching to see if the fault origin's address matches one of the TPS. In this case, the error is a TPS protection fault, otherwise, it is a standard segfault. The test file further asserts that the proper behavior is exhibited by each function under its specific failing conditions.

## Sources:
-https://stackoverflow.com/
-https://piazza.com/
-Porquet's Lecture and Discussion Slides
