# Condition Variables

- There are many cases where a thread wishes to check whether a
    condition is true before continuing its execution
- A naive approach could just constantly check a global (spin)
  - hugely inefficient as this wastes CPU time

## Definition and Routines

- To wait for a condition to become true, a thread can make use of
    what is known as a condition variable.
- A condition variable is an explicit queue that threads can put
    themselves on when some state of execution (i.e., some condition) is
    not as desired (by waiting on the condition)

## Simple Example

    1 int done = 0;
    2 pthread_mutex_t m = PTHREAD_MUTEX_INITIALIZER;
    3 pthread_cond_t c = PTHREAD_COND_INITIALIZER;
    4
    5 void thr_exit() {
    6   Pthread_mutex_lock(&m);
    7   done = 1;
    8   Pthread_cond_signal(&c);
    9   Pthread_mutex_unlock(&m);
    10 }
    11
    12 void *child(void *arg) {
    13  printf("child\n");
    14  thr_exit();
    15  return NULL;
    16 }
    17
    18 void thr_join() {
    19  Pthread_mutex_lock(&m);
    20  while (done == 0)
    21  Pthread_cond_wait(&c, &m);
    22  Pthread_mutex_unlock(&m);
    23 }

## Semantics

- When checking your condition use a while loop (Mesa Semantics)
- Hoare semantics, is harder to build but provides a stronger
    guarantee that the woken thread will run immediately upon being
    woken
- Virtually every system ever built employs Mesa semantics.

## Spurious Wakeups

In some thread packages, due to details of the implementation, it is
possible that two threads get woken up though just a single signal has
taken place. Spurious wakeups are further reason to re-check the
condition a thread is waiting on.
