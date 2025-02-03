# Synchronize(동기화)와 Deadlock

### Synchronized 키워드와 동기화

#### synchronized 란?

* **멀티쓰레딩 환경에서 공유 자원(Shared Resource) 에 대한 동기화(Synchronization)를 제공** 하는 키워드임.
* 여러 쓰레드가 동시에 공유 데이터에 접근하면 <mark style="background-color:blue;">**경쟁 조건(Race Condition)**</mark> 이 발생할 수 있음.
* 이를 방지하기 위해 <mark style="background-color:blue;">**임계 구역(Critical Section)**</mark> 을 설정하여 한 번에 하나의 쓰레드만 해당 블록을 실행할 수 있도록 함.

#### synchronizeed 사용방법

* Java 에서 `synchronized` 는 **메서드 단위** 또는 **블록 단위** 로 사용할 수 있음.

1. `synchronized` 메서드

```java
class SharedResource {
    private int count = 0;
    
    public synchronized void increament() {
        count++;
    }
    
    public synchronized int getCount() {
        return count;
    }
}
```

* 위와 같이 `synchronized` 키워드를 메서드에 사용하면 **해당 메서드가 실행되는 동안 다른 스레드는 이 메서드를 실행할 수 없음**&#x20;
* 즉, `increment()` 메서드가 실행되는 동안 다른 스레드는 `increment()` 나 `getCount()` 를 호출할 수 없음.

2. `synchronized` 블록

```java
class SharedResource {
    private int count = 0;
    private final Object lock = new Object(); // 별도의 Lock 객체 생성
    
    public void increment() {
        synchronized (lock) {
            count++;
        }
    }
    
    public int getCount() {
        synchronized (lock) {
            return count;
        }
    }
}
```

* 특정 객체(`lock` ) 에 대해 동기화 블록을 설정하면 **해당 객체를 사용하는 다른 synchronized 블록들이 같은 객체에 접근하지 못하게 제한** 함.
* `synchronized` 블록을 사용하면 **동기화 범위를 줄일 수 있어 성능 최적화** 가 가능함.

3. 정적 메서드의 `synchronized`&#x20;

```java
class SharedResource {
    private static int count = 0;
    
    public static synchronized void increment() {
        count++;
    }
    
    public static synchronized int getCount() {
        return count;
    }
}
```

* 정적 메서드에 `synchronized` 를 적용하면 **클래스 레벨의  동기화** 가 수행됨.
* 즉, `SharedResource.class` 자체가 락을 가지므로 **해당 클래스의 모든 인스턴스가 공유하는 자원에 대한 동기화가 보장** 됨.

***

### synchronized 와 Deadlock(교착 상태)

#### Deadlock(교착 상태) 란?

Deadlock(교착 상태)이란 **두 개 이상의 스레드가 서로가 가진 리소스를 기다리면서 영원히 멈춰있는 상태** 를 말함.

**Deadlock 조건**

1. **상호 배제(Mutual Exclusion)** : 한 번에 하나의 스레드만 공유 자원을 사용할 수 있어야 한다.
2. **점유 대기(Hold and Wait)** : 하나의 쓰레드가 자원을 점유한 상태에서 다른 자원을 기다려야 한다.
3. **비선점(No Preemption)** : 점유한 자원을 다른 쓰레드가 강제로 가져갈 수 없어야 한다.
4. **순환 대기(Circular Wait)** : 두 개 이상의 스레드가 서로를 기다리는 형태의 순환 구조가 존재해야 한다.

#### Deadlock 발생 예제

아래 코드에서는 `Thread-1` 과 `Thread-2` 가 각각 다른 락을 가지고 있으며, **서로 상대방이 가진 락을 기다리는 상태** 가 되어 Deadlock 이 발생할 수 있음.

```java
class Resource {
    public void print(String message) {
        System.out.println(message);
    }
}

class Task implements Runnable {
    private final Resource resource1;
    private final Resource resource2;
    
    public Task(Resource resource1, Resource resource2) {
        this.resource1 = resource1;
        this.resource2 = resource2;
    }
    
    @Override
    public void run() {
        synchronized (resource1) {
            resource1.print(Thread.currentThread().getName() + " locked resource1");
            
            try{
                Thread.sleep(100);
            }catch(InterruptedException e){}
            
            synchronized(resource2) {
                resource2.print(Thread.currentThread().getName() + " locked resource2";
            }
        }
    }
}

public class DeadlockExample {
    public static void main(String[] args) {
        Resource resourceA = new Resource();
        Resource resourceB = new Resource();
        
        Thread thread1 = new Thread(new Task(resourceA, resourceB), "Thread-1");
        Thread thread2 = new Thread(new Task(resourceB, resourceB), "Thread-2");
        
        thread1.start();
        thread2.start();
    }
}
```

**설명**

* `Thread-1` 은 `resourceA` 를 먼저 획득한 후 `resourceB` 를 기다림.
* `Thread-2` 는 `resourceB` 를 먼저 획득한 후 `resourceA` 를 기다림.
* 결과적으로 두 쓰레드는 서로 상대방이 가진 자원을 기다리며 Deadlock 이 발생하게됨.

#### Deadlock 해결 방법

1. 락을 항상 일정한 순서로 획득하기

```java
synchronized (resourceA) {
    synchronized (resourceB) {
        // 작업 수행
    }
}
```

* 모든 쓰레드가 `resourceA` -> `resourceB` 순서로 락을 요청하면 Deadlock 이 발생하지 않음.

2. `tryLock()` 을 사용하여 시간 초과 설정하기 (ReentrantLock 활용)

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

class SafeTask implements Runnable {
    private final Lock lock1;
    private final Lock lock2;
    
    public SafeTask(Lock lock1, Lock lock2) {
        this.lock1 = lock1;
        this.lock2 = lock2;
    }
    
    @Override
    public void run() {
        while(true) {
            try{
                if(lock1.tryLock() && lock2.tryLock()){
                    try{
                        System.out.println(Thread.currentThread().getName() + " acquired both locks";
                        break;
                    } finally {
                        lock2.unlock();
                        lock1.unlock();
                    }
                }
            } finally {
                if(lock1.isHeldByCurrentThread()) lock1.unlock();
                if(lock2.isHeldByCurrentThread()) lock2.unlcok();
            }
        }
    }
}

public class DeadlockPrevention {
    public static void main(String[] args) {
        Lock lockA = new ReentrantLock();
        Lock lockB = new ReentrantLock();
        
        Thread thread1 = new Thread(new SafeTask(lockA, lockB), "Thread-1");
        Thread thread2 = new Thread(new SafeTask(lockB, lockA), "Thread-2");
        
        thread1.start();
        thread2.start();
    }
}
```

* `tryLock()` 을 사용하면 특정 시간 동안만 락을 대기하고, 시간이 지나면 락을 포기함.

3.Deadlock 감지 및 해결 알고리즘 사용

* 탐욕적 알고리즘 : 점유한 락을 주기적으로 확인하여 일정 시간 이상 유지되면 해제하는 방법.
* Wait-for Graph 알고리즘 : 시스템의 락 대기 그래프를 분석하여 사이클이 발생하면 Deadlock 을 감지하는 방법
