# Java 멀티스레드와 병렬처리

## 1. Thread

### 1.1 Thread 생성과 실행(Thread 클래스 vs Runnable 인터페이스)

자바에서 새로운 Thread 를 만드는 기본 방법은 `Thread` 클래스를 직접 사용하거나 `Runnable` 인터페이스를 구현하는 것임. 두 방식 모두 `run()` 메서드에 작업 내용을 정의하고, `start()` 메서드로 Thread 를 시작함.

* **Thread 클래스 상속** : `Thread` 클래스를 상속받아 `public void run()` 메서드를 오버라이드함. 그런 다음 객체를 생성하고 `start()` 를 호출하면 새로운 Thread 가 생성되어 `run()` 메서드의 코드가 실행됨.

```java
//Thread 클래스를 상속하여 스레드 정의
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("새 스레드 실행 중: " + Thread.currentThread().getName());
    }
    
// 스레드 생성 및 시작
MyThread thread1 = new MyThread();
thread1.start(); // run() 메서드 실행 시작
```

위 코드에서 `thread1.start()` 를 호출하면 **새 스레드** 가 생성되어 `MyThread.run()` 의 내용을 병렬로 수행함. `run()` 을 직접 호출하면 새로운 스레드가 아니라 현재 스레드에서 메서드만 실행되므로, 반드시 `start()` 를 사용해야함.

* **Runnable 인터페이스 구현** : 클래스가 다른 클래스를 이미 상속 중이라면 `Thread` 를 상속하기 어려움. 이 경우 `Runnable` 을 구현하거나 람다식으로 `Runnable` 을 만들어 `Thread` 생성자에 주입하는 방식을 주로 사용함.&#x20;

```java
//Runnable 구현체 정의
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Runnable 쓰레드 실행 중: " + Thread.currentThread().getName());
    }
}

//Runnable을 이용한 스레드 생성 및 시작
Runnable task = new MyRunnable();
Thread thread2 = new Thread(task);
thread2.start();

// 람다 표현식으로 Runnable 구현
Thread thread3 = new Thread(() -> {
    System.out.println("람다 쓰레드 실행 중 : " + Thread.currentThread().getName());
});
thread3.start();
```

위 코드에서 `thread2` 와 `thread3` 는 각각 `Runnable` 을 통해 정의된 작업을 새로운 스레드에서 수행함. 일반적으로 **Thread 를 상속하기보다는 Runnable 구현** 을 사용하는 것이 권장되는데, 이는 자바는 다중 상속을 지원하지 않기 때문임. Runnable 을 사용하면 다른 클래스를 상속받으면서도 스레드 작업을 정의할 수 있고, 또한 스레드 풀 등 **Executor 프레임워크** 와도 쉽게 연계할 수 있음.

### 1.2 Thread 의 LifeCycle (생명주기와 상태)

`Thread` 는 생성부터 종료까지 여러 **상태(state)** 를 거침. 자바 언어 명세에서 정의한 스레드의 상태는 6가지이며, `Thread.State` 열거형으로도 제공됨.

> Java 스레드의 라이프사이클 상태 다이어그램 : 스레드는 `New` 상태에서 시작하여 `Runnable` (실행 가능) 상태로 이동하고, 실행 중(active) 일 수 있음. 동기화 락을 획득하지 못하면 `Blocked` , `wait()` 등으로 대기하면 `Waiting` (또는 시간 제한이 있는 `Time Waiting` ), 실행이 종료되면 `Terminated` 상태가 됨.<img src="../../.gitbook/assets/image.png" alt="" data-size="original">

1. **NEW (새 스레드)** - 스레드 객체를 생성했지만 `start()` 로 실행을 시작하지 않은 초기 상태임. 이때까지는 코드 실행을 시작하지 않은 상태임.
2. **RUNNABLE** ( 실행 가능 상태) - `start()` 호출 후 실행 중이거나 실행 가능한 상태를 말함. 자바에서는 **Running** 상태를 별도로 구분하지 않고 Runnable 로 통칭하는데, 스레드가 실제 CPU 에서 실행 중이거나 OS 스케줄러에 의해 실행 대기 중인 상태 모두 포함됨.
3. **BLOCKED** (블로킹 상태) - 스레드가 모니터 락(monitor lock) 을 얻지 못해 **진입 대기** 중인 상태임. 예를 들어 어떤 객체의 `synchronized` 블록에 들어가려는데 다른 스레드가 락을 선점한 경우, 락을 얻을 때까지 BLOCKED 상태로 대기함.
4. **WAITING** ( 무기한 대기 ) - 다른 스레드의 특정 동작을 **무기한 기다리는** 상태임. `Object.wait()` 이나 `Thread.join()` (타임아웃 없는 경우) 를 호출했을 때, 또는 `LockSupport.park()` 등을 호출하면 WAITING 상태가 됨. 해당 상태에서는 다른 스레드가 notify 하거나 해당 스레드가 깨어날 조건이 충족될 때까지 기다림.
5. **TIMED\_WAITING** (시간 대기) - 일정 시간 동안 기다리는 상태임. `sleep(n)` 이나 `Object.wait(n)` 처럼 **타임아웃이 있는 대기** 호출을 하면 TIME\_WAITING 상태로 지정 시간 동안 대기하게 됨. 시간이 만료되거나 notify 를 받으면 다시 Runnable 로 돌아감.
6. **TERMINATED** (종료) - 스레드 실행이 종료된 상태임. `run()` 메서드가 정상적으로 끝나거나, 예외로 인해 종료될 경우 이 상태가 됨. TERMINATED 상태의 스레드는 다시 실행될 수 없음. (하나의 `Thread` 객체는 한 번만 start 가능).

스레드 상태는 `Thread.getState()` 메서드로 런타임에 조회할 수 있음. 다만 이 상태 정보는 **시점** 에 따라 변할 수 있고, 디버깅이나 모니터링 용도로만 참고해야함. 일반적으로 개발 시에는 위 상태들을 이해하고, 특히 BLOCKED, WAITING 등이 왜 발생하는지 파악하는 것이 중요함.

**상태 전이** : NEW ⇒ (start 호출) ⇒ RUNNABLE 로 이동하며 스케줄링에 따라 실행됨. 실행 중에 `synchronized` 락을 얻지 못하면 BLOCKED, `wait()` 호출 시 WAITING, `sleep(timeout)` 호출 시 TIMED\_WAITING 상태로 전이됨. `notify()` 를 받아 깨거나 시간이 만료되면 다시 RUNNABLE 로 돌아옴. `run()` 메서드가 리턴되면 TERMINATED 가 됨. 이러한 라이프사이클을 이해하면, 예를 들어 스레드가 응답하지 않을 때 **스레드 덤프** 를 통해 BLOCKED 나 WAITING 상태에 머물러 데드락에 빠졌는지 등을 진단할 수 있음.

### 1.3 Thread 동기화 (synchronized, Lock, volatile)

다중 스레드 환경에서 **공유 자원** 에 대한 동시 접근을 제어하지 않으면 **레이스 컨디션(race condition)** 이 발생할 수 있음. 자바는 기본적인 동기화 수단으로 `synchronized` 키워드와 `volatile` 키워드, 그리고 좀 더 고급의 Lock API 를 제공함.

* **synchronized 블록/메서드** : 가장 기본적인 **상호 배제(mutual exclusion)** 방법임.\
  `synchronized` 를 사용하면 한 순간에 **오직 한 스레드** 만 해당 블록을 실행할 수 있도록 보장됨. `synchronized(obj)` 블록은 `obj` 객체의 **모니터 락** 을 획득한 스레드만 진입하며, 종료 시 락을 해제함. 예를 들어 공유객체 `counter` 를 여러 스레드가 증가시키는 경우:&#x20;

```java
class Counter {
    private int count = 0;
    public synchronized void increment() {
        count++;
    }
    public synchronized int getCount() {
        return count;
    }
}
```

위 코드는 `increment()` 와 `getCount()` 에 **synchronized** 를 적용하여, 한 번에 하나의 스레드만 `count` 값을 변경하거나 읽을 수 있음. 메서드 저체에 synchronized 를 붙이면 해당 객체 (`this` ) 를 락으로 사용하며, 블록으로도 지정할 수 있음. `synchronized` 를 잘 활용하면 간단한 **임계구역(critical section)**   보호에 충분하지만, 범위를 최소화하여 락 경쟁을 줄이는 것이 성능에 유리함.

* **Lock 인터페이스** (java.util.concurrent.locks.Lock) : `synchronized` 의 기능을 확장한 **명시적 락** 임. 가장 많이 쓰이는 구현은 `ReentrantLock`  으로, 동일 스레드가 중첩해서 락을 획득할 수 있고(`synchronized` 와 동일), `tryLock()` (락 획득 시도) 이나 `lockInterruuptibly()` (인터럽트에 반응) 등의 추가 기능을 제공함. 사용법은 다음과 같음

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;
...
Lock lock = new ReentrantLock();
lock.lock();
try {
    // 임계 구역 - 공유자원 접근
} finally {
    lock.unlock();
}
```

`lock.lock()` 과 `unlock()` 호출은 반드시 **try-final** 로 묶어서, 예외가 발생해도 락이 해제되도록 하는 것이 중요함. `ReentrantLock` 은 공정성(fairness) 설정이나 Condition 객체로 세분화된 wait/notify 등 고급 제어도 가능하지만, 기본적으로는 synchronized 와 유사한 용도로 쓰임.

* **volatile 키워드** : `volatile` 은 변수에 대한 **메모리 가시성(memory visibility)** 을 보장함. 한 스레드가 volatile 변수 값을 변경하면 즉시 다른 스레드에게 변경 내용이 보장되는 것을 의미함. 일반 변수는 스레드마다 **캐시된 값** 을 사용하거나 재정렬 될 수 있지만, volatile 은 이러한 최적화를 제한하여 항상 **최신 값을 읽도록** 함. 다만 volatile은 원자적 연산을 보장하지는 않기 때문에, ++ 연산과 같이 읽고 쓰는 복합 연산에는 여전히 동기화가 필요함. 주로 **플래그 변수** (예 : `boolean stopped` ) 등에 사용되어 한 스레드가 값을 변경하면 다른 스레드가 즉시 감지하도록 하는 용도로 활용됨.

```java
class Worker extends Thread {
    private volatile boolean running = true;
    public void run() {
        while(running) {
            // 작업 수행
        }
    }
    public void stopRunning() {
        running = false;
    }
}   
```

위 코드에서 `running` 을 volatile 로 선언하여, 다른 스레드가 `stopRunning()` 을 통해 `false` 로 설정하면 `run()` 루프에서 즉시 그 변화를 감지하고 종료하게 됨. 만약 volatile 이 없다면 `running` 값이 캐시에 남아 루프가 바로 끝나지 않을 수 있음.



**동기화 고려사항** : 여러 스레드가 동시에 접근하는 데이터를 보호하기 위해 꼭 필요한 최소한의 영역에만 동기화를 적용하는 것이 좋음. 불필요하게 큰 범위를 동기화하면 병렬성이 떨어지고 **경합(contention)** 이 증가함. 또한, 한 객체를 락으로 사용하는 synchronized 에서는 **죽음의 락** 을 피하기 위해 신중해야 함. 예를 들어 `String` 상수나 전역 객체에 대해 synchronized 를 걸면 의도치 않게 광범위한 락 대기가 발생할 수 있음.

추가로, JDK의 `AtomicInteger` , `ConcurrentHashMap` 등 **원자적 연산 클래스와 동시성 컬렉션** 을 활용하면 보다 높은 수준에서 스레드 안전성을 확보할 수 있음. 이러한 클래스들은 내부적으로 최적화된 락이나 CAS(Compare-And-Swap) 로 동기화 문제를 해결하므로, 직접 `synchronized` 를 사용하는 것보다 편리하고 성능상 이점이 있는 경우가 많음.

### 1.4 wait() / notify() / notifyAll() 활용 (스레드 간 협업)

자바에서 스레드 간에 조건이 충족될 때까지 기다리거나 깨우는 메커니즘으로 `wait()` 와 `notify()` , `notifyAll()` 메서드를 제공함. 이들은 `Object` 클래스에 정의되어 있으며, 모든 객체를 모니터 락으로 활용할 수 있게 함. `wait()` 은 현재 스레드를 대기 상태로 만들고, `notify()` 는 대기 중인 스레드 하나를 깨우며, `notifyAll()` 은 대기 중인 모든 스레드를 깨움. 사용 시에는 반드시 해당 객체의 **모니터를 소유한 상태** (즉, `synchronized` 블록 또는 메서드 안) 에서 호출해야 하며, 그렇기 않으면 `IllegalMonitorStateException` 이 발생함. 예를 들어, 하나의 스레드가 어떤 **조건** 을 만족할 때까지 기다리고, 다른 스레드가 그 조건을 충족시킨 후 깨우는 간단한 예제임.

```java
class MessageBox {
    private String message;
    public synchronized String getMesssage() throws InterruptedException {
        // 메시지가 설정될 때까지 기다림.
        while(message == null) {
            System.out.println("메시지 스레드: 메시지 대기 중...");
            wait(); // message 가 준비될 때까지 대기
        }
        return message;
    }
    public synchronized void setMessage(String msg) {
        this.message = msg;
        System.out.println("알림 스레드: 메시지 설정 완료, notify 호출!");
        notify();
    }
}
// 사용 예:
MessageBox box = new MeesageBox();
Thread waiter = new Thread(() -> {
    try {
        String result = box.getMessage();
        System.out.println("메시지 스레드: 전달 받은 메시지 = " + result);
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
});
Thread notifier = new Thread(() -> {
    try {
        Thread.sleep(1000);
    } catch(InterruptedException e) {}
    box.setMessage("안녕하세요");
});
waiter.start();
notifier.start();
```

위 코드에서 `waiter` 스레드는 `message` 가 `null` 인 동안 `wait()` 으로 **WAITING 상태** 에 들어가 대기함. `notifier` 스레드는 1초 후 `setMessage` 를 호출하여 메시지를 설정하고 `notify()` 를 호출함. 이 때 `wait()` 로 기다리던 스레드가 깨워져(`notify` 는 하나의 스레드만 깨움) 루프를 빠져나와 메시지를 출력함.



`notifyAll()` 은 여러 스레드가 한 객체에서 기다릴 경우 모두 깨울 때 사용함. 예를 들어 다수의 소비자 스레드가 `wait()` 으로 대기하고 있고 하나의 생산자 스레드가 데이터 추가 후 전부 꺠우고 싶다면 `notifyAll()` 을 사용함. 일반적으로 **다중 스레드의 조건 대기** 에서는 `wait()` 와 `while` 루프를 함께 사용하여(spurious wake-up 대비) 조건이 충족될 때까지 반복 대기하고, `notify/notifyAll` 로 깨우는 패턴을 사용함.

> **실무 팁** : 직접 `wait()/notify()` 를 사용하는 저수준 동기화는 버그를 만들기 쉬움. 가능하면 **고수준 동시성 도구** 인 `BlockingQueue` (예 : `ArrayBlockingQueue` 로 생산자-소비자 구현)나 `CountDownLatch` , `Semaphore` 등을 사용해 스레드 협업을 구현하는 것이 좋음. 이러한 도구들은 내부에서 적절히 `wait/notify` 등을 사용해 스레드 협업을 구현하는 것이 좋음. 이러한 도구들은 내부에서 적절히 `wait/notify` 를 활용하고 사용법을 단순화하여, 개발자가 직접 모니터를 관리하는 실수를 줄여줌.

### 1.5 Thread-safe 코드 작성법

멀티스레드 환경에서 **Thread-Safe** 한 코드를 작성하기 위해서는 공유 자원 접근을 철저히 관리해야함. 다음은 실무에서 유용한 스레드 안정성 확보 전략임.

* **공유 상태 최소화** : 가능한 한 **불변 객체(Immutable)** 를 활용하거나, 스레드마다 별도의 객체 인스턴스를 사용해서 **공유되는 가변 상태(mutablestate)** 를 줄이는 것이 중요함. 공유되는 전역 변수나 컬렉션의 사용을 최소화하고, 메서드 로컬 변수나 파라미터로 값을 전달하는 식으로 설계를 단순화함.
* **동기화 대상 최소화** : 여러 스레드가 반드시 공유해야 하는 자원이 있다면 . 그 **임계구역** 만 좁게 동기화함. 큰 메서드 전체를 동기화하기보다는 진짜 필요한 부분만 `synchronized` 로 보호하고, 나머지는 병렬 실행이 가능하도록 해줌. 이렇게 하면 불필요한 대기 시간을 줄일 수 있음.
* **일관된 락 순서** : 두 개 이상의 락을 사용하는 상황이라면 **항상 동일한 순서로 락을 획득** 하도록 설계하여 **데드락(deadlock)** 을 예방함. 예를 들어 자원 A와 B를 모두 잠글 필요가 있는 경우, 스레드마다 항상 A ⇒ B 순서로 락을 걸고 해제도 반대로 하면 교차 대기가 발생하지 않음. 가능하다면 중첩된 락 사용을 피하고, 복잡한 락 순서가 필요하면 Lock 객체의 `tryLock(longtimeout)` 등을 활용해 교착상태를 감지/회피하는 것도 고려함.
* **스레드 안전 컬렉션과 원자적 연산 활용** : JDK 가 제공하는 `ConcurrentHashMap` , `CopyOnWriteArrayList` 등 동시성 컬렉션과 `AtomicInteger` ,`LongAdder` 등의 원자적 연산 도구를 적절히 활용. 예를 들어 다중 스레드가 접근하는 HashMap 은 `ConcurrentHashMap` 으로 대체하고, `++` 연산은 `AtomicInteger.incrementAndGet()` 으로 대체하면, 내부적으로 최적화된 동기화가 적용되어 성능과 안전성 모두 향상됨.
* **InterruptedException 처리** : 스레드의 `interrupt` 신호나 `InterruptedException` 발생을 무시하지 말고 적절히 대응해야함. 대개 반복 루프에서 `InterruptedException` 이 발생하면 루프를 탈출하거나 현재 스레드를 종료시키는 방향으로 처리함. 이는 스레드 풀이나 상위 로직에서 스레드를 취소(cancel) 하기 쉽게 해주며, 자원을 깔끔하게 정리할 수 있게 해줌.
* **기타 주의사항** : `Thread.sleep()` 이나 I/O 대기처럼 **오래 걸리는 작업을 수행할 때는** 해당 스레드를 오래 잡아두지 않도록 설계함. GUI 애플리케이션에서는 특히 이벤트 디스패치 스레드(EDT) 를 차단하지 않고, 별도 워커 스레드에서 작업하고 결과를 EDT 에 전달하는 패턴을 사용함. 또한 `Thread.stop()` 과 같은 Deprecated 된 메서드는 사용하지 않고, interrupt 나 상태 플래그를 통한 정상적인 종료를 구현함.

***

## 2. 고급 병렬 처리 및 최적화 전략

### 2.1 Executor 프레임워크(Executor, ExecutorService, ThreadPoolException)

전통적으로 `new Thread()` 를 통해 직접 스레드를 생성하고 시작하는 방식은 작은 예제에서는 간단하지만, **실무 환경** 에서는 비효율적이고 관리가 어려움. Java5 부터 도입된 **Executor 프레임워크 (java.util.concurrent 패키지)** 는 **스레드 풀(Thread Pool)** 을 활용하여 스레드 생명주기를 관리하고 작업 실행을 효율화 함.

* **Executor/ExecutorService** : `Executor` 는 실행(Runnable 실행) 의 추상화를 나타내는 인터페이스이고, `ExecutorService` 는 `Executor` 를 확장한 인터페이스로, 스레드 풀의 생명주기 관리(shutdown 등) 와 Future 를 통한 결과 획득 등의 기능을 포함함. 일반적으로 `Executors` 유틸리티 클래스의 정적 팩토리 메서드를 통해 ExecutorService 를 생성함.

```java
ExecutorService pool = Executors.newFixedThreadPool(4); // 고정 크기(4개) 스레드 풀 생성
for (int i = 0; i < 10; i++) {
    pool.execute(() -> {
        // 병렬로 실행할 작업 내용
        System.out.println(Thread.currentThread().getName() + "작업 수행");
    });
}
pool.shutdown(); // 더 이상은 새로운 작업을 받지 않고, 기존 작업 완료 후 종료
```

위 코드에서는 4개의 스레드를 가진 풀을 생성하고, 10개의 작업을 제출했음. 풀의 스레드가 작업을 나누어 병렬로 실행하며, `shutdown()` 을 호출하면 추가 작업을 받지 않고 제출된 작업들을 모두 끝마친 후 스레드를 종료함.(`shutdownNow()` 를 호출하면 진행 중인 작업을 interrupt 하고 즉시 종료를 시도함.)

* **스레드 풀의 장점** : 스레드 생성을 **재사용** 하여, 작업마다 스레드를 새로 만드는 오버헤드를 줄일 수 있음. 스레드 생성에는 시간과 메모리(스택 등)가 드는데 풀을 사용하면 일정 개수의 스레드를 미리 만들어 놓고 작업만 교체하면서 수행하므로 효율적임. 또한 스레드 풀이 **과도한 스레드 생성을 억제** 하여, 시스템이 감당할 수 없는 수의 스레드가 생성되는 것을 막아줌.
* **ThreadPoolExecutor 튜닝** : `Executors.newFixedThreadPool` 등 팩토리 메서드는 내부적으로 `ThreadPoolExecutor` 을 설정된 값으로 생성함. `ThreadPoolExecutor` 클래스를 직접 생성하면 코어 풀 크기(corePoolSize), 최대 풀 크기(maximumPoolSize), keep-alive 시간 (유휴 스레드 종료 시간), 작업 대기열(queue) 등을 세밀하게 조절할 수 있음. 예를 들어 :&#x20;

```java
ExecutorService customPool = new ThreadPoolExecutor(
    2, 5, 60, TimeUnit.SECONDS,
    new ArrayBlockingQueue<>(100)
);
```

위 풀은 코어 스레드 2개, 최대 5개 까지 늘어나며, 작업 대기큐는 100개까지 버퍼링할 수 있고, 60초 동안 유휴(idle) 상태인 추가 스레드는 제거함. **실무에서는** CPU 코어 수나 작업 특성에 맞게 스레드 풀 크기 등을 조정하여 **최적의 처리량** 을 달성함. 예컨대 CPU 바운드 작업만 있다면 코어 수 이상 스레드를 늘려도 성능 향상이 거의 없고, 오히려 컨텍스트 스위칭만 증가시킴. 반면 I/O 바운드 작업이 많다지만 대기 시간 동안 CPU 를 다른 작업에 사용할 수 있도록 스레드 수를 더 늘릴 여지가 있음.

* **실무 팁** : `ExecutorService` 를 사용할 때는 **반드시 종료** 를 시켜줘야 함. `shutdown()` 이나 `shutdownNow()` 를 호출하지 않고 놔두면 자바 프로세스가 종료되지 않거나, 애플리케이션이 계속해서 자우너을 점유할 수 있음. 또한 `Executors.newCachedThreadPool` 이나 직접 ThreadPoolExecutor 로 최대치를 설정하는 것이 안전함.

### 2.2 Callable 과 Future (작업 결과 처리)

기존의 `Runnable` 은 반환값이 없고 예외를 전팔할 수 없다는 한계가 있음. **Callable** 은 제네릭 타입의 반환값을 가지는 태스크를 정의할 수 있는 함수 인터페이스로서, 작업을 실행한 후 **결과를 반환** 하거나 예외를 던질 수 있음. `Callable<V>` 의 `call()` 메서드는 `V` 타입 결과를 반환하며, 예외 발생 시 던진 예외가 상위로 전달됨.

**Future** 는 비동기 작업의 결과를 나중에 받아볼 수 있는 **약속** (promise) 객체임.

`ExecutorService.submit(Callable)` 을 호출하면 `Future` 를 얻는데, 이 Future 를 통해 작업 완료 여부 조회, 결과 획득, 작업 취소 등을 할 수 있음.

```java
// Callable 정의 : 1 부터 N 까지 합계 계산 후 반환
Callable<Long> sumTask = () -> {
    long sum = 0;
    for(int i = 1; i <= 1000000; i++) {
        sum += i;
    }
    return sum;
};

ExecutorService pool = Executors.newFixedThreadPool(2);
Future<Long> future = pool.submit(sumTask); // Callable 제출
// 다른 작업 수행 가능(논블로킹)

// 결과가 필요할 때
try{
    Long result = future.get(); // 결과가 준비될 때까지 대기 (블로킹)
    System.out.println("합계 결과: " + result);
} catch(InterruptedException e) {
    Thread.currentThread().interrupt(); // 인터럽트 시 현재 스레드 상태 보존
} catch(ExecutionException e) {
    Throwable cause = e.getCause();
    System.err.println("작업 실행 중 예외 발생: " + cause);
}
```

위 코드에서 `future.get()` 을 호출하면 해당 작업이 완료될 때까지 현재 스레드는 **블로킹** 됨. `get()` 은 작업이 끝나면 결과값을 반환하고, 작업 중 예외가 발생했다면 `ExecutionException` 형태로 던져지므로 원인 예외는 `getCause()` 로 확인함. 또한 `future.cancel(true)` 를 호출하면 작업을 취소(진행 중인 스레드를 `interrup` ) 할 수 있으며, `isDone()` 으로 완료 여부, `isCancelled()` 로 취소 여부를 확인할 수도 있음.

**활용 시나리오** : Callable 과 Future 는 **병렬 계산** 결과를 모으거나, **타임아웃** 을 적용한 대기 등에 쓰임. 예를 들어 여러 개의 Callable 을 `invokeAll()` 로 한꺼번에 제출하여 모든 작업이 끝날 때까지 기다린 뒤 결과를 모울 수 있고, `invokeAny()` 를 쓰면 여러 작업 중 가장 빨리 끝난 결과를 가져오기도 함. 또한 `Future.get(long timeout, TimeUnit)` 메서드를 사용하면 정해진 시간 내에 결과가 오지 않을 경우 `TimeoutException` 으로 빠져나와 대비할 수 있음.

* **실무 팁** : 너무 많은 작업을 개별 Callable 로 제출하면 오버헤드가 커질 수 있으므로 적절한 작업 크기로 묶는 것이 좋음. 또한 Future 를 얻은 뒤 결과를 바로 `get()` 으로 기다리기만 한다면 사실상 **동기 호출과 다름없으므로**, 그 사이 다른 유용한 작업을 하거나, 아니면 애초에 비동기로 처리할 필요가 있는 작업인지 고민해야함.

### 2.3 Fork/Join 프레임워크( 분할 정복 병렬 처리 )

Fork/Join 프레임워크는 **분할 정복 알고리즘** 에 특화된 병렬 처리 프레임워크로, 자바 7에서 도입되었음. 큰 작업을 작은 작업으로 재귀적으로 분할(fork) 하고, 처리된 결과를 합치는 (join) 방식으로 동작하며, 내부적으로 **워크스틸링(work-stealing)** 스케줄링을 사용하여 효율적으로 쓰레드 자원을 활용함. `ForkJoinPool` 은 ExecutorService 의 일종이며, `RecursiveTask<V>` (결과를 반환) 나 `RecursiveAction` (void 반환) 클래스를 상속하여 태스크를 정의함.

**배열 합계** 를 Fork/Join 으로 계산하는 간단한 예제:

```java
import java.util.concurrent.RecursiveTask;
import java.util.concurrent.ForkJoinPool;

class SumTask extends RecursiveTask<Long> {
    private final int[] arr;
    private final int start, end;
    private static final int THRESHOLD = 1000; // 이 이하의 크기는 순차 처리
    
    SumTask(int[] arr, int start, int end) {
        this.arr = arr;
        this.start = start;
        this.end = end;
    }
    
    @Override
    protected Long compute() {
        int length = end - start;
        if (length <= THRESHOLD) {
            //작은 조각은 순차적으로 합계 계산
            long sum = 0;
            for(int i = start; i < end; i++) {
                sum += arr[i];
            }
            return sum;
        } else {
            // 큰 문제는 두 부분으로 분할
            int mid = start + length/2;
            SumTask leftTask = new SumTask(arr, start, mid);
            SumTask rightTask = new SumTask(arr, mid, end);
            leftTask.fork(); // 왼쪽 절반 비동기 실행
            long rightResult = rightTask.compute(); // 오른쪽 절반 즉시 실행
            long leftResult = leftTask.join(); // 왼쪽 결과 대기 및 합류
            return leftResult + rightResult;
        }
    }
}

// 사용 예:
ForkJoinPool pool = ForkJoinPool.commonPool();  // ForkJoinPool 인스턴스 (공통 풀)
int[] bigArray = new int[10000];
// ... (배열 초기화) ...
SumTask task = new SumTask(bigArray, 0, bigArray.length);
long total = pool.invoke(task);
System.out.println("총 합계: " + total);
```

위 예제에서 `SumTask` 는 `RecursiveTask<Long>` 를 상속하여 compute() 내에서 작업을 분할하거나 결과를 반환함.`THRESHOLD` 를 기준으로 분할 여부를 결정하여, 일정 크기 이하면 순차 처리하고, 크면 반으로 나눠서 두 작업으로 나눔. `fork()` 는 새로운 작업을 **비동기로 실행** 하도록 보내고 (`pool` 의 스레드가 알아서 처리), `compute()` 또는 `join()` 을 통해 다른 부분을 처리/대기 함. 최종적으로 `invoke()` 를 사용하면 해당 작업이 완료되고 결과를 반환할 때까지 현재 스레드가 기다림. (또는 `pool.execute(task)` 로 비동기로 수행할 수도 있음)



Fork/Join 은 주로 **재귀적 알고리즘** (퀵소트, 병렬 merge sort, 피보나치, 행렬 계산 등) 에 사용됨. 내부 구현이 워크스틸링으로 최적화되어 있어, 각 스레드가 자신에게 할당된 deque 작업 큐를 처리하고, 빈 스레드는 다른 스레드의 남은 작업을 훔쳐오는 방식으로 **부하 균형** 을 맞춤. 다만, Fork/Join 을 사용할 때는 **분할에 따른 오버헤드** 를 고려해 THRESHOLD 값을 적절히 설정해야함. 너무 잘게 분할하면 오히려 스레드 관리 비용 때문에 성능이 떨어질 수 있으므로, 작업당 일정량 이상의 계산이 있도록 조율함.

* **실무 팁** : Java 8 부터 일반적인 병렬 처리는 굳이 Fork/Join 을 직접 사용할 필요 없이 **병렬 스트림** 이 나 `CompletableFuture` 로 대체되는 경우가 많음. 그러나 Fork/Join  은 커스텀한 분할정복이 필요할 때 여전히 유용함. 또한, 애플리케이션 전체에서 공유되는 ForJoinPool 의 **공통 풀(commonPool)** 을 사용할지, 별도로 `new ForkJoinPool(n)` 으로 풀을 만들어 사용할지 결정해야 함. 공통 풀은 기본적으로 프로세서 코어 수만큼의 스레드를 갖는데, 병렬 스트림 등과 공유하므로 대규모 작업은 전용 풀을 사용하는 것이 다른 병렬 작업에 영향을 주지 않는데 유리함.

### 2.4 병렬 스트림(Parallel Streams)

Java 8 의 스트림 API 는 데이터 처리 파이프라인을 선언적으로 작성할 수 있게 해주는데, 스트림은 **내부적으로 반복** 을 처리하므로 병렬 실행도 쉽게 할 수 있음. `stream()` 으로 생성한 스트림을 `.parallel()` 중간 연산을 호출하거나 처음부터 `parallelStream()` 을 호출하면 스트림의 연산을 **병렬** 로 수행함.

```java
List<Integer> numbers = IntStream.rangeClosed(1, 100000)
                                 .boxed()
                                 .collect(Collectors.toList());
                                 
// 순차 스트림 합계
long sum1 = numbers.stream().mapToLong(i -> i).sum();

// 병렬 스트림 합계
long sum2 = numbers.parallelStream().mapToLong(i -> i).sum();
```

위 코드는 같은 리스트에 대해 순차 스트림과 병렬 스트림으로 합계를 계산하는 예시임. 병렬 스트림은 내부적으로 **ForkJoinPool 의 공통 툴** 을 사용하여 여러 스레드에서 리스트를 분할 처리한 뒤 결과를 합침. 일반적으로 CPU 코어 수에 비례하여 병렬 작업을 나눠 수행하므로, 계산량이 충분히 크다면 병렬 스트림이 더 빠른 결과를 낼 수 있음.

그러나 **모든 경우에 병렬 스트림이 이로운 것은 아님** . 병렬화에도 오버헤드가 있기 때문에, 입력 데이터가 아주 적거나, 처리 과정이 I/O 바운드라서 CPU를 크게 사용하지 않는다면 오히려 병렬화로 인한 이득이 없을 수 있음. 또한 스트림의 **중간 연산이 비싸거나** 병렬처리에 적합하지 않은 경우(예 : 요소 간에 의존성이 있거나, 순서가 중요한 연산)에는 주의가 필요함. `forEachOrdered` 를 사용하면 병렬 스트림이라도 순서를 보장하느라 성능 이점을 잃게 됨.

병렬 스트림은 **공유 mutable 상태를 피해야** 함. 람다 내부에서 외부 변수에 누적하는 작업 등을 하면 병렬 실행 시 동기화 문제가 발생할 수 있으므로, 가급적 순수 함수형으로 작성하거나 `reduce` , `collect` 등을 사용해서 Thread-Safe 한 수집기를 사용해야 함. 스트림 API 가 알아서 병렬 처리를 관리해주므로 개발자는 간단히 `.parallel()` 호출만으로 병렬화를 적용할 수 있지만, **병렬 처리의 효과와 위험성** 은 여전히 개발자의 몫이므로, 성능 프로파일과 결과의 정확성을 검증하는 것이 중요함.

* **실무 팁** : 병렬 스트림은 간편하지만, 제어할 수 있는 부분이 제한적임. 기본적으로 사용하는 ForkJoinPool 의 스레드 수는 `Runtime.getRuntime().availableProcessors()` 로 정해지는데, 이 값을 바꾸려면 시스템 프로퍼티(`java.util.concurrent.ForkJoinPool.common.parallelism` ) 를 설정해야함. 만약 별도의 큰 병렬 작업이 동시에 돌아간다면 공통 풀의 리소스를 놓고 경합할 수 있으므로, 그런 경우 앞서 언급한 ForkJoinPool 을 직접 만들어 사용하는 방법을 고려하는게 좋음. 또한, 병렬 스트림은 **스트림 소스의 분할특성** 에 영향을 받는데, `ArrayList` 나 기본 배열은 분할이 효율적이지만 `LinkedList` 처럼 분할이 비효율적인 컬렉션은 병렬 처리가 그다지 형성되지 않을 수 있음.

### 2.5 CompletableFuture 와 비동기 프로그래밍

`CompletableFuture` 는 자바 8 에 추가된 기능으로, **비동기 작업의 파이프라인화** 를 가능하게 해주는 Future 의 확장판임. 이전의 Future 는 단순히 결과를 얻기 위해 기다리는 정도였지만, CompletableFuture 는 **콜백 체이닝** 을 통해 결과가 들어오면 자동으로 다음 동작을 수행하는 식의 선언적 비동기 프로그래밍을 지원함.

* **비동기 작업 실행** : `CompletableFuture.supplyAsync()` 나 `runAsync()` 정적 메서드를 사용하면 ForkJoinPool 의 공통 풀(또는 제공된 Executor) 에서 비동기로 작업을 수행함. 예를 들어:

```java
CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
    // 긴 계산 또는 I/O 호출
    try { Thread.sleep(500); } catch (InterruptedException e) {}
    return 42;
});
```

위 코드는 별도 스레드에서 0.5 초 동안 실행되는 작업을 시작하고, 곧바로 Future 를 반환함. 메인 스레드는 블로킹되지 않고 다른 일을 할 수 있음.

* **롤백 연계** : `thenApply` , `thenAccept` ,`theRun` , `thenCompose` , `thenCombine` 등의 메서드를 이용하여 앞선 작업 결과를 가지고 다음 동작을 정의할 수 있음. 예를 들어, 위에서 얻은 결과에 1을 더하는 작업을 이어서 정의했음.

```java
CompletableFuture<Integer> resultFuture = future.thenApply(result -> {
    System.out.println("첫 작업 결과: " + result);
    return result + 1;
});
```

여기서 `future` 가 완료되면 자동으로 `thenApply` 의 람다가 실행되어 `result` 에 1을 더하고, 그 결과가 `resultFuture` 에 담김. 이러한 방식으로 비동기 작업들을 **순차적으로** 또는 **조합하여** 실행할 수 있음. `thenCompose` 는 앞선 결과를 사용해 또 다른 비동기 작업을 연결(Flat Map) 할 때, `thenCombine` 은 두 개의 독립적인 Future 결과를 조합할 때 사용함. 예:

```java
CompletableFuture<Integer> f1 = CompletableFuture.supplyAsync(() -> { /* 계산 A */ });
CompletableFuture<Integer> f2 = CompletableFuture.supplyAsync(() -> { /* 계산 B */ });
CompletableFuture<Integer> combine = f1.thenCombine(f2, (a,b) -> a + b);
combine.thenAccept(sum -> System.out.println("A+B 결과: " + sum));
```

위 코드는 계산 A와 B를 병렬로 실행하고, 둘 다 완료되면 합계를 출력함. **중간에 블로킹하지 않고** 콜백으로 이어가기 때문에, 전체 작업 흐름이 논블로킹으로 유지됨.

* **예외 처리** : `CompletableFuture` 에는 `exceptionally` (예외 발생 시 대체값 지정)나 `handle` (정상/예외 모두 처리) 메서드를 통해 비동기 연산 도중 발생한 예외도 처리할 수 있음. 이는 일일이 try-catch 를 하는 것보다 흐름 제어상 편리함.
* **커스텀 Executor 사용** : 기본적으로 `CompletableFuture` 의 비동기 메서드는 ForkJoinPool 의 commonPool 을 사용하지만, `supplyAsync(Supplier, Executor)` 처럼 두 번째 인자로 Executor를 넘겨주면 사용자 지정 스레드 풀에서 실행할 수도 있음. 대규모 I/O 작업을 처리하거나 UI 스레드와의 연계를 위해 **별도 Executor 를 지정** 하는 것이 성능 및 응답 관리에 유리할 수 있음.

**실무 활용** : `CompletableFuture` 는 비동기 작업을 구성하는 강력한 도구임. 예를 들어, 웹 서비스 호출을 병렬러 하고 모든 응답을 모아서 처리하거나, 하나의 요청에 대해 여러 서비스를 조회한 후 취합하는 경우 등에 유용함. 또한 순차적으로 의존적인 비동기 작업을 코드 블로킹 없이 수행할 수 있으므로, **콜백 지옥(callback hell)** 없이도 복잡한 비동기 로직을 구현할 수 있음. 다만 남용하면 오히려 디버깅이 어려워질 수 있고, ForkJoinPool 의 공통 스레드를 과점유하면 다른 작업에 영향을 줄 수 있으므로, 상황에 맞게 적절히 사용해야함.

### 2.6 성능 최적화 및 주의할 점(컨텍스트 스위칭, 데드락, 스레드 수 튜닝 등)

멀티스레드와 병렬 처리는 성능 향상의 열쇠이지만, 반대로 잘못 사용하면 문제를 일으킬 수 있음. 다음은 **고성능 병렬 처리** 를 위한 최적화 포인트와 주의사항임.

* **컨텍스트 스위칭 오버헤드** : CPU 가 한 스레드에서 다른 스레드로 **문맥 전환(context switch)** 할 때는, 레지스터나 메모리 맵 등의 상태를 저장하고 복구하는 비용이 듬. 따라서 **스레드 수를 과도하게 늘리면** 오히려 컨텍스트 스위칭 비용이 커져 성능이 떨어질 수 있음. 예를 들어 CPU 코어가 4개인데 활성 스레드가 100개라면, 각 코어가 수시로 스레드를 바꿔가며 실행해야 하므로 오버헤드가 큼. 가능한 한 **작업량에 맞게 적절한 스레드 수** 를 유지하는 것이 중요하며, 이것이 바로 스레드 풀을 사용하는 주된 이유이기도 함.(스레드 재사용 및 제한).
* **스레드 수 튜닝(CPU vs I/O)** : 작업이 CPU 바운드인지 I/O 바운드인지에 따라 최적의 스레드 수가 달라짐. **CPU 바운드 작업** ( 계산 위주 ) 은 일반적으로 CPU 코어 수와 맞춰 스레드 수를 설정하는 것이 좋음. (예: 8코어 머신에서 8개 스레드). 코어 수 이상의 스레드는 동시에 실행되지 않고, 컨텍스트 스위칭만 증가시킴. 반면 **I/O 바운드 작업** (디스크/네트워크 대기)은 스레드가 외부 자원 응답을 기다리는 동안 CPU 를 사용하지 않으므로, CPU 코어 수보다 더 많은 스레드를 둘 수도 있음. 다만, I/O 바운드라고 해서 무제한 늘릴 수 있는 것은 아니며, 각 스레드의 메모리 사용(스택 등) 과 JVM 스케줄링 부담도 고려해야함. **적정 수준** 을 찾기 위해 애플리케이션을 모니터링 하면서 조정하는 것이 바람직함.
* **데드락(교착 상태)** 과 **라이브락** : 두 스레드가 서로 상대방이 가진 락을 기다릴 때 데드락이 발생하며, 영원히 대기 상태에 빠짐. 이 문제를 피하려면 앞서 언급한 **일관된 락 획득 순서** 를 유지하고, 가능하면 락을 세분화하기 보다는 필요한 자원을 하나의 락으로 관리하거나, 때에 따라 **타임 아웃 락** 이나 **락 백오프(back-off)** 전략을 사용하는 것이 좋음. `java.util.concurrent` 의 고수준 구조들을 이용하면 데드락 위험을 낮출 수 있음. 예를 들어 `ConcurrentHashMap` 은 세분화 된 락을 내부에서 관리하여 사용자 코드에서 별도의 락을 걸 필요 없이 병렬 접근이 가능함. **라이브락** 은 지속적으로 상태가 변하면서 진행이 안 되는 경우인데(서로 양보만 하여 진척 없음). 이는 보통 잘못된 협업 로직에서 발생하므로, 알고리즘 설계를 신중히 해야함.
* **자원 고갈 및 병목 식별** : 스레드 풀의 사이즈를 너무 작게 잡으면 CPU 가 놀거나 (I/O 가 대기가 아닌데도), 너무 크게 잡으면 문맥전환과 메모리 소비가 커짐. 적절한 **병목 지점** 을 찾아야 함. 예를 들어, 작업 처리 파이프라인에서 특정 단계가 가장 느리면 그 부분을 병렬화하거나 리소스를 늘려주는 것이 효과적임. 툴링으로는 **스레드 덤프(Thread Dump)** 나 **프로파일러** 를 활용해 어떤 스레드/락에서 대기가 발생하는지, CPU 사용률을 어떤지 관찰하면됨. 특히 애플리케이션이 **정체(stall)** 되었을 때 스레드 덤프를 보면 특정 객체 락에서 많은 스레드가 BLOCKED 인 경우 등을 발견할 수 있음.
* **GPU/메모리와의 조화** : 자바의 멀티스레딩은 CPU를 활용하는 것이지만, 경우에 따라 GPU 연산이나 외부 시스템과의 연계도 고려됨. 순수 자바 병렬화로 CPU를 100% 사용하는 것이 항상 최선은 아니며, **적절한 병렬 알고리즘** ( 예 : 대용량 데이터 가공은 맵리듀스 모델 도입 등) 이나 **비동기 I/O** (NIO, Netty 활용) 로 전환하는 것이 나을 수 있음. 즉, 스레드의 수를 무작정 늘리기보다, 애플리케이션 구조 차원에서 병렬 처리를 최적화해야 함.
* **최신 기능 활용** : 자바 19+ 에서는 가벼운 스레드인 **가상 스레드(Virtual Threads)** 가 도입되어, 수천 수만 개의 동시 실행을 보다 적은 비용으로 다룰 수 있게 되었음. 이는 전통적인 OS 스레드의 한계(컨텍스트 스위칭 비용 등) 을 극복하기 위한 최신 기술이지만, 아직 표준으로 자리잡는 단계임. 실무에서는 **현재 LTS 버전의 안정적 기능** (Executors, CompletableFuture 등) 을 우선 활용하되, 새로운 기법도 지속적으로 추적하여 적절한 시점에 도입하는 것이 좋음.
* **테스트와 프로파일링** : 병렬 처리 최적화의 궁극은 **경험과 측정** 임. 일반적인 가이드라인은 참고하되, 애플리케이션의 특성에 따라 최적의 스레드 전략은 달라짐. 따라서 다양한 시나리오에서 부하 테스트를 실시하고, \*\*스레드 풀 크기 변화에 따른 처리량, 응답시간을 측정해보는 것이 좋음. 또한 CPU, 메모리, GC 등의 지표를 함께 모니터링하여 병렬화로 인한 부작용은 없는지 점검해야함.



*
