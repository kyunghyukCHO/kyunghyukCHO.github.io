---
layout: single
title:  " [운영체제] Semaphores "
---

Lock + Condition Variables = Semaphore
===
semaphore 는 lock 과 condition variables 를 모두 기능할 수 있습니다. 

int sem_init(sem_t *s, int pshared, unsigned int value)
---
* 만약 pshared 가 0 이라면, semaphore 는 프로세스 내의 스레드들 간에 공유됩니다. 
* 그렇지 않다면, semaphore 는 프로세스들 간에 공유됩니다. 또한 공유 메모리에 존재해야 합니다.
* unsigned int value : Lock 혹은 Condition Variable

int sem_wait(sem_t *s)
---
* semaphore 의 value 값을 1 감소시킵니다. 
* 만약 semaphore 의 value 가 음수가 된다면 sleeping 상태가 됩니다. 

int sem_post(sem_t *s)
---
* semaphore 의 value 값을 1 증가시킵니다. 
* 만약 waiting 중인 스레드들이 있다면 하나를 깨웁니다. 

Senaphores for Ordering
---

```c++
sem_t s;
void * child(void *arg) {
    printf("child\n");
    sem_post(&s);
    return NULL;
}
int main(int argc, char *argv[]) {
    pthread_t c;
    sem_init(&s, 0, X); // what should X be?
    printf("parent: begin\n");
    pthread_create(&c, NULL, child, NULL);
    sem_wait(&s);
    printf("parent: end\n");
    return 0;
}
```

한가지 예외사항을 고려한다면, wait이 불러졌을 때 sleeping이 되어야 합니다. wait이 불러지기 전에 post가 호출되면 value +=1 이 됩니다
이후에 wait이 불러졌을 때 value -=1 값이 음수가 된다면 sleeping 됩니다. 이때, post가 이미 호출됐으므로 해당 thread 가 계속 잠들게 됩니다. 결론적으로, 모든 상황을 고려할 때, semaphore 의 초기 value 는 0이 되어야 합니다.

Semaphores in Producer/Consumer Problem
---
buffer 의 state 정보를 가리키는 count 변수가 필요하지 않습니다. semaphore 변수 empty 와 full 을 선언합니다. 이 semaphore 변수들이 결국 state 정보를 내포하며 count 변수의 역할을 수행합니다.     
* empty -=1 음수가 아니라면 put(i) 후에 full +=1
* full 변수가 양수인경우 get() 후에 empty +=1     
하지만 mutiple producer and consumer 인 경우 race condition 이 발생합니다. 그 해결책으로 mutex 라는 semaphore 변수를 1로 초기화 하여 한개 더 선언한 뒤 한번 더 감싸게 됩니다. 이는 실제 mutex lock 은 아니지만 같은 기능을 하게 됩니다. Race condition 은 이렇게 해결했지만 또 다른 문제가 생기는데, 그 문제가 Deadlock 문제 입니다.

DeadLock
---
```c++
void *producer(void *arg) {
    int i;
    for (i = 0; i < loops; i++) {
        sem_wait(&empty);
        sem_wait(&mutex);
        put(i);
        sem_post(&mutex);
        sem_post(&full);    
    }
}
void *consumer(void *arg) {
    int i;
    for (i = 0; i < loops; i++) {
        sem_wait(&full);
        sem_wait(&mutex);
        int tmp = get();
        sem_post(&mutex);
        sem_post(&empty);
    }
}
```

여러 thread 가 있을 때, 한 thread 가 현재 기다리는 상태를 해결해 줄 수 있는 thread 가 다른 thread라면
서로를 기다리기만 하게 되고 멈춰있는 상태 입니다.     

과정
---
consumer -> mutex semaphore -=1 == 0 -> wait -> full -=1 -> sleeping -> producer -> mutex sema -=1 == -1 -> sleeping -> 이를 해결해 줄 수 있는건 consumer의 sem_post -> 하지만 post 가 불리기 위해선 full 이 증가해야함 -> full 은 producer 가 put 해줘야함 -> 하지만 잠들어있음.. -> Deadlock (교착상태) 발생

Solution
---
mutex semaphore 를 empty 와 full semaphore 안쪽으로 배치시키는 것 입니다.


Reader-Writer Locks
---
Reader 의 lock 함수
Writer 의 lock 함수

```c++
typedef struct _rwlock_t {
    // binary semaphore (basic lock)
    sem_t lock;
    // used to allow ONE writer or MANY readers
    sem_t writelock;
    // count of readers reading in critical section
    int readers;
} rwlock_t;

void rwlock_init(rwlock_t *rw) {
    rw->readers = 0;
    sem_init(&rw->lock, 0, 1);
    sem_init(&rw->writelock, 0, 1);
}

void rwlock_acquire_writelock(rwlock_t *rw) {
    sem_wait(&rw->writelock);
}
void rwlock_release_writelock(rwlock_t *rw) {
    sem_post(&rw->writelock);
}

void rwlock_acquire_readlock(rwlock_t *rw) {
    sem_wait(&rw->lock);
    rw->readers++;  
    if (rw->readers == 1)
        // first reader acquires writelock
        sem_wait(&rw->writelock);
    sem_post(&rw->lock);
}
void rwlock_release_readlock(rwlock_t *rw) {
    sem_wait(&rw->lock);
    rw->readers--;
    if (rw->readers == 0)
        // last reader releases writelock
        sem_post(&rw->writelock);
    sem_post(&rw->lock);
}
```

장점
---
공유되는 자료구조에 대해 여러개의 readers 가 동시에 접근해 race condition 없이 concurrency 를 늘려줄 수 있습니다. writer 끼리는 writerlock에 의해 동기화되므로 race condition이 발생하지 않습니다. 

문제점
---
하지만 writer가 reader에 비해 우선순위가 밀리는 문제점이 발생할 수 있습니다. 예를들어 reader가 계속해서 진입한다면 중간에 writer가 작업이 필요해도 reader 가 0이 될때까지 writer 가 starvation 하게 됩니다.


How to implement Semaphore (CODE)
---
```c++
typedef struct __Sem_t {
    int value;
    pthread_cond_t cond;
    pthread_mutex_t lock;
} Sem_t;
// only one thread can call this
void Sem_init(Sem_t *s, int value) {
    s->value = value;
    Cond_init(&s->cond);
    Mutex_init(&s->lock);
}

void Sem_wait(Sem_t *s) {
    Mutex_lock(&s->lock);
    while (s->value <= 0)
        Cond_wait(&s->cond, &s->lock);
    s->value--;
    Mutex_unlock(&s->lock);
}
void Sem_post(Sem_t *s) {
    Mutex_lock(&s->lock);
    s->value++;
    Cond_signal(&s->cond);
    Mutex_unlock(&s->lock);
}
```