# User-Level Thread Library by Grayson and Kat Arnott

## Phase 1: Semaphore API
  Our semaphore struct was created to include two member variables, one for the count, and one associated queue of tasks waiting for a semaphore resource. Appropriately, our '''sem_up''' and '''sem_down''' functions appropriately increment or decrement the 'count' of available resources for a given semaphore, and will either enqueue and block a thread, or dequeue and process a thread (if waiting and next in line).
  
  However, there comes an issue where a thread (Thread A) is asleep, waiting for some resource, and it is called to wake and access the next resource once it becomes available, but this resource is taken by some other thread first (Thread B). In order to handle this scenario, we created a critical section in '''sem_down''', such that a thread would be pushed onto the semaphore queue when there were no resources available and be blocked. Whenever a thead in this critical section awakens, it will recheck if there is a resource available before taking it. In this way, if some Thread B takes the resource, Thread A will go back to sleep and both will not try to proceed with insufficient resources.

## Phase 2: TPS API
The goal of the TPS API is to enable private storage for each thread in a process, such that no thread can read from or write to the memory table of another thread.

To create Thread Private Storage, we implemented a TPS struct containing the tid of the thread using it, and the address of the memory page for that thread.




## Testing:


## Sources:

