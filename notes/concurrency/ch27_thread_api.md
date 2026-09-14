## Ch 27: Thread API
### Summary:
### Terms:
- pthread_create 
    - arguments:
        - thread:
            - this is a thread handler object that is passed into the pthread_create function to be initialized
        - attr:
            - configuration that we pass to the OS like stack size or priority
        - start_routine:
            - function pointer to the routine that we want the thread to execute
            - often received and returns arguments of type void pointer that can be cast to their actual type
        - arg:
            - arguments to the function that is passed 
    - a mechanism by which to spawn a thread
- pthread_join:
    - a mechanism by which to wait for the thread to complete
    - takes as input the thread handle and a pointer to the return value of the thread
        - pointer must be passed by reference so that it may be modified
        - actually passes a pointer to a pointer to the return type
    - if you return from a thread a pointer to a piece of memory that is allocated on the call stack of the thread then that memory will not be available anymore after the thread exits
        - you will have a dangling pointer
- locks:
    - pthread_mutex_t
    - pthread_mutex_lock:
        - this is a function that takes a reference to a lock type
        - this function will not return until the lock is free and it can be acquired
        - if the lock is currently held by another thread, this function will block the current thread until it is released
    - locks must be initialized by the OS so that their defaults are correct
        - don't want to create a closed lock
- condition variables:
    - a way for threads to communicate with each other
    - threads can wait on conditions to be true 
    - threads can signal to waiting threads that it is time to wake up
    - rely on the scheduler to sleep / wake waiting threads instead of busy waiting on flags
### Questions: