---
description: CompletableFuture, Future 등등..
---

# 비동기

### 비동기

* CompletableFuture java 5 -> Future 라는 비동기를 가능하게 해주는 클래스가 있었음. 하지만 문제점이 많음.

1. 예외 처리용 API를 제공하지 않음.
2. 여러 Future 조합이 어려움
3. Future를 외부에서 완성 시킬 수 없다.

3번에서 나온 외부에서 완성 시킬 수 없다는 예시로 보여줌

```
ExecutorService executor = Executors.newSingleThreadExecutor();
Future<Integer> future = executor.submit(() -> {
    // 비동기 작업 수행
    return 42;
});

// 외부에서 작업 완료를 표시할 수 없음
// future.complete(42); // 이와 같이 완료시킬 수 없음

future.get();  // 이 부분은 작업이 끝날 때 까지 대기하는 메서드
```

CompletableFuture 는 외부에서 작업을 종료 시킬 수 있음

```
CompletableFuture<Integer> future = new CompletableFuture<>();

// 외부에서 작업을 완료시킴
future.complete(42); // 결과 값을 설정하여 작업 완료

// 또는
// future.completeExceptionally(new RuntimeException("작업 실패")); // 작업 예외로 완료

// 작업 완료 후 결과를 처리하는 콜백 등록
future.thenAccept(result -> System.out.println("작업 결과: " + result));
future.exceptionally(ex -> {
    System.out.println("작업 실패: " + ex.getMessage());
    return null;
});
```

병렬처리

ExecutorService 를 통해 스레드 생성 후 CompletableFuture를 통해 비동기로 여러개의 스레드를 돌릴 수 있음 (_밥먹으면서 휴대폰도 보고 발도 씻을 수 있음!_)

CompletableFuture 는 값을 반환 받을 수 있는 supplyAsync(), void 인 runAsync() 가 있음.

작업의 결과(return) 이 있냐 없냐의 차이 이므로 알맞게 사용하면 된다.

이 작업들은 두개의 오버로드 된 형태가 있는데, 하나는 스레드 풀을 지정, 다른 하나는 실행할 비동기 작업만을 받는 형태이다.

병렬처리 하기 위해서는 thread 풀을 지정해서 하는 방법을 추천한다.

```
public class ParallelProcessingExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(4);

        CompletableFuture<Integer> future1 = CompletableFuture.supplyAsync(() -> {
            // 비동기 작업 1 수행
            return 42;
        }, executor);

        CompletableFuture<Integer> future2 = CompletableFuture.supplyAsync(() -> {
            // 비동기 작업 2 수행
            return 100;
        }, executor);

        CompletableFuture<Integer> combinedFuture = future1.thenCombine(future2, (result1, result2) -> {
            // 두 작업의 결과를 조합하여 새로운 결과 생성
            return result1 + result2;
        });

        combinedFuture.thenAccept(result -> {
            System.out.println("작업 결과: " + result);
            executor.shutdown();
        });
    }
}
```

### 병렬처리의 문제점 (dead lock)

dead lock 이 발생하는 조건

1. 자원 동시 접근 불가
   * 한번에 여러 프로세스(혹은 쓰레드) 가 한 자원에 접근하지 못하도록 막음
2. 점유하고 기다리기
   * 자원을 가지고 있는 상태에서 다른 프로세스가 쓰는 자원을 반납하기를 기다리는 상태
3. 자원 강탈 불가
   * 다른 프로세스가 이미 점유한 자원을 가져오지 못함
4. 순환 종속성
   * 각 프로세스(혹은 쓰레드)가 서로를 대기하는 상태

#### 해결 방법은?

**데드락 회피** : 위에서 나온 조건 처럼 코딩 안하면 된다.

**데드락 탐지와 회복** : 데드락 발생을 막지 않음. 발생하면 해결함

**데드락 무시** : 데드락 예방에는 성능상 손해를 봐야한다. 그래서 무시도 하나의 방법이다.(무시해도 될만한 녀석이라면)

데드락 회피에 대한 예시

```
public class DeadlockPreventionExample {
    public static void main(String[] args) {
        CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> {
            // 비동기 작업 1 수행
            return "Hello";
        });

        CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> {
            // 비동기 작업 2 수행
            return "World";
        });

        CompletableFuture<String> combinedFuture = future1.thenCombineAsync(future2, (result1, result2) -> {
            // 두 작업의 결과를 조합하여 새로운 결과 생성
            return result1 + " " + result2;
        });

        combinedFuture.thenAccept(result -> {
            System.out.println("결과: " + result);
        });

        // CompletableFuture의 완료를 기다림
        combinedFuture.join();
    }
}
```

위의 예시 코드에서 future1과 future2는 각각 "Hello"와 "World"라는 결과를 생성하는 비동기 작업을 수행한다. 이후 thenCombineAsync() 메서드를 사용하여 두 작업의 결과를 조합하여 "Hello World"라는 새로운 결과를 생성하는 CompletableFuture인 combinedFuture를 생성한다.

thenAccept() 메서드에서는 최종 결과를 출력하고, join() 메서드를 호출하여 CompletableFuture의 완료를 기다린다. join() 메서드는 CompletableFuture가 완료될 때까지 블로킹된다.

이 예시 코드에서는 순환 종속성을 피하고, 두 개의 작업이 순차적으로 실행되도록 보장된다. 이를 통해 데드락을 방지할 수 있다.
