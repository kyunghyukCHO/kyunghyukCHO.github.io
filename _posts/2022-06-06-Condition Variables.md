---
layout: single
title:  " [운영체제] Condition Variables "
---

Condition Variables
===
thread 가 특정 state 를 만족하지 않으먼 Queue 로 보내져 sleeping 하게 해줍니다. Other thread 가 그 state 를 만족하도록 해주면 Queue 에 들어있는 thread 를 깨웁니다.     

* pthread cond wait : 잠들 때     
    * lock 을 풀어주고 atomical 하게 thread sleeping 호출을 합니다. 
    * thread 가 다시 깨어날때 lock 을 다시 얻습니다
* pthread cond signal : 깨울 때       

메인함수에서 child thread 를 생성했다고 가정하겠습니다. child thread 가 특정 상태를 만족시켜주기 전까지 main thread 가 spin을 돌며 상태를 지속적으로 check 합니다. 이때, cpu 자원을 낭비하고 비효울적이게 됩니다. 이러한 문제를 해결해주기 위해 condition variables 를 일반적으로 사용합니다. wait 혹은 signal 함수의 인자값으로 condition variables 를 넣어주게 되는데 이는 대기중인 Queue 를 명시하게 됩니다. cond_wait 에는 인자값으로 mutex 를 넘겨받게 됩니다. 함수 내에서 unlock 을 시키고 재우고 시그널에 의해 깨어나게 되면 lock 을 다시 얻고 return 하게 됩니다.

Example 
---
* child -> thr_exit() -> signal()
* main -> thr_join() -> wait()

```python
int done = 0;
pthread_mutex_t m = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t m = PTHREAD_COND_INITIALIZER;
void *child(void *arg) {
    printf("child\n");
    the_exit();
    return NULL;
}

int main(int argc, char *argv[]) {
    pthread_t p;
    printf("parent : begin\n");
    pthread_create(&p, NULL, child, NULL);
    thr_join();
    printf("parent : end\n");
    return 0;
}

void thr_exit() {
    pthread_mutex_lock(&m);
    done = 1;
    pthread_cond_signal(&c);
    // 같은 큐를 대상으로 스레드를 깨움 
    pthread_mutex_unlock(&m);
}

void thr_join() {
    pthread_mutex_lock(&m);
    while (done ==0) {
        pthread_cond_wait(&c,&m); 
        // main thread 가 잠들게 됨
        // wait 함수가 리턴되면서 done 이 바뀌었으로 while문 탈출
    }
    pthread_mutex_unlock(&m);
}
```

* signal 과 wait 앞 뒤로 mutex_lock 을 해주게 됩니다. 
* wait 이 만족하는 문법은 if 가 아닌 while 문 입니다.
* wait 에서 인자값에 mutex 를 전달하는 이유는 wait 전에 lock 이 호출되므로 wait으로 잠들기 전에 언락을 시켜 signal 을 부를 수 있는 thread 가 exit 함수의 critical section 으로 진입할 수 있습니다. 그 이후 깨어날 때 다시 lock 해줍니다.

만약 state variable 이 없다면?
---
**예를들어 done value 를 빼버린다면?**
만약 child thread 를 생성하고 join()을 부르기 전에 CPU scheduler 는 cpu 자원을 child 에게 넘겨주게 됩니다. main thread 는 child 에서 exit()을 부릅니다. Queue 에 잠들어있는 thread 를 깨우려고 하지만 잠들어있는 thread 는 없습니다. 그러므로 그냥 return 되고 child thread 는 종료됩니다. CPU 자원이 main thread 로 넘어가서 join 함수를 호출하게 되면 main thread 가 잠들게 되고 깨워 줄 child thread 가 이미 signal 호출 후 return 됐기 때문에 main thread 가 영원히 잠들게 되고 응용프로그램이 종료되지 않습니다.

만약 lock 이 없다면?
---
chlid thread 생성 -> thread join() -> while 문이 종료되고 wait 이 호출되기 전에 스케쥴러가 시피유 자원을 차일드 스레드에게 넘겨줘버리게 됩니다. -> 앞선 문제와 같은 문제가 반복됩니다. 

Application
===

Producers and Consumers
---

* Producers : 데이터 생산 스레드 
* consumers : 데이터 소비 스레드 
Producer 와 consumer 가 동시에 있을때 어떻게 관리할지에 대한 문제입니다. 생산자들은 bounded buffer 에 데이터를 넣습니다. 일종의 큐라고 보면 됩니다. 소비자 입장에서는 큐에서 데이터를 꺼내가게 됩니다. 이러한 관계에 condition variables 를 적절히 사용할 수 있습니다. 
이것들은 예를들어 Pipe 혹은 Web servers 에서 사용됩니다. boudned buffer 는 shared resource 이기 때문에 동기화된 접근을 얻어야 합니다. 

```python
int buffer; // 큐 사이즈 1 = 싱글버퍼
int count = 0;
void put(int value) {
    assert(count ==0);
    count = 1
    buffer = value;
}
int get() {
    assert(count == 1);
    count = 0;
    return buffer;
}
```

Example - if
---
```python
cond_t cond;
mutex_t mutex;
void *producer(void *arg) {
    int i;
    for (i = 0; i < loops; i++) {
        pthread_mutex_lock(&mutex); // p1
        if (count == 1) // p2 if 문 ?
            pthread_cond_wait(&cond, &mutex); // p3
        put(i); // p4
        pthread_cond_signal(&cond); // p5
        pthread_mutex_unlock(&mutex); // p6
    }
}

void *consumer(void *arg) {
    int i;
    for (i = 0; i < loops; i++) {
        pthread_mutex_lock(&mutex); // c1
        if (count == 0) // c2
            pthread_cond_wait(&cond, &mutex); // c3
        int tmp = get(); // c4
        pthread_cond_signal(&cond); // c5
        pthread_mutex_unlock(&mutex); // c6
        printf("%d\n", tmp);
    }
}
```

Not If But While
---
생산자 1개 그리고 소비자 2개가 있다고 가정해봅시다.(p1, c1, c2) 생산자가 데이터를 하나 생산하고 signal 을 호출하고 그때 잠들어있던 c1 이 깨어납니다. 하지만 그 전에 c1은 깨어나기만 하고 ready state 에 있고 생산자가 다시 loop을 돌았을 때 권한을 c2에게 주게 되면 c2가 데이터를 소진해버려서 c1에서 가져올 데이터가 없게 되고 오류가 나게 됩니다. 즉, c1이 ready state 가 됐으나 시간이 흘러 다시 cpu 자원을 얻을 때 그 state 가 바뀔수도 있습니다. 그러므로 if문이 아닌 while문으로 조건을 유지시켜야 합니다.

One condition variables problem
---
같은 condition variables 안에 생산자도 잠들 수 있고 소비자도 잠들 수 있습니다. 이때 시그널을 보냈을 때 누구를 깨워야 할지 모르는 문제점이 있습니다. 소비자들은 다른 소비자들을 깨워선 안됩니다. 오직 생산자들만 깨워주어야 합니다. 이를 해결하기 위해 condition variables 를 두개 생성해주는 방법이 있습니다.








