# Sort

정렬(Sorting) 은 데이터를 특정 기준에 따라 순서대로 배열하는 작업으로, 많은 알고리즘 문제나 실제 개발에서 자주 등장함. Java 에서도 다양한 정렬 알고리즘을 직접 구현하거나 라이브러리를 통해 사용할 수 있음.&#x20;

**기본 정렬 알고리즘(버블, 선택, 삽입)** 부터 **고급 정렬 알고리즘(퀵, 병합, 힙)** 까지 각 알고리즘의 개념, 시간 복잡도, Java 코드 예제를 정리하고 성능을 비교해보겠음.

***

## 기본 정렬 알고리즘

### 1. 버블 정렬(Bubble Sort)

* **개념** : 인접한 두 값을 비교해서 잘못된 순서라면 교환. 한 바퀴마다 가장 큰 수가 뒤로 이동함.
*   **시간 복잡도** :&#x20;

    * 최선 : $$O(n)$$
    * 평균/최악 : $$O(n^2)$$


* **공간 복잡도** : $$O(1)$$
* **특징** : 단순하고 안정 정렬이지만 성능이 가장 떨어짐.

```java
public static void bubbleSort(int[] arr) {
    for(int i = 0; i < arr.length - 1; i++) {
        boolean swapped = false;
        for(int j = 0; j < arr.length - i - 1; j++) {
            if(arr[j] > arr[j + 1]) {
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
                swapped = true;
            }
        }
        if (!swapped) break;
    }
}
```

***

### 2. 선택 정렬(Selection Sort)

* **개념** : 남은 부분 중 가장 작은 값을 골라 앞쪽과 교환
* **시간 복잡도** : 항상 $$O(n^2)$$
* **공간 복잡도** : $$O(1)$$
* **특징** : 교환 횟수가 적지만 불안정 정렬.

```java
public static void selectionSort(int[] arr) {
    for(int i = 0; i < arr.length - 1; i++) {
        int minIndex = i;
        for(int j = i + 1; j < arr.length; j++) {
            if(arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }
        int temp = arr[i];
        arr[i] = arr[minIndex];
        arr[minIndex] = temp;
    }
}
```

***

### 3. 삽입 정렬(Insertion Sort)

* **개념** : 앞에서부터 정렬된 부분에 새 값을 알맞은 위치에 삽입.
*   **시간 복잡도** :&#x20;

    * 최선 : $$O(n)$$ (이미 정렬된 경우)
    * 평균 / 최악 : $$O(n^2)$$


* **공간 복잡도** : $$O(1)$$
* **특징** : 거의 정렬된 배열에서 빠름. 안정 정렬.

```java
public static void insertSort(int[] arr) {
    for(int i = 1; i < arr.length; i++) {
        int key = arr[i];
        int j = i - 1;
        while(j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
}
```

***

## 고급 정렬 알고리즘

### 4. 퀵 정렬(Quick Sort)

* **개념** : 기준(pivot) 을 정해 좌우로 분할하고 재귀적으로 정렬
* **시간 복잡도** :&#x20;
  * 평균 : $$O(n log n)$$
  * 최악 : $$O(n^2)$$
* **공간 복잡도** : $$O(log n)$$ (재귀 스택)
* **특징** : 매우 빠르며 가장 널리 사용됨. 불안정 정렬

```java
public static void quickSort(int[] arr, int low, int high) {
    if(low < high) {
        int pi = partition(arr, low, high);
        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}

private static int partition(int[] arr, int low, int high) {
    int pivot = arr[high];
    int i = low - 1;
    for(int j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            i++;
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
    }
    int temp = arr[i + 1];
    arr[i + 1] = arr[high];
    arr[high] = temp;
    return i + 1;
}
```

***

### 5. 병합 정렬(Merge Sort)

* **개념** : 배열을 반으로 나누고 정렬 후 병합
* **시간 복잡도** : 항상 $$O(n logn)$$
* **공간 복잡도** : $$O(n)$$ (추가 배열 필요)
* **특징** : 안정 정렬, 메모리를 많이 사용하지만 성능 일정.

```java
public static void mergeSort(int[] arr, int left, int right) {
    if(left < right) {
        int mid = (left + right) / 2;
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}

private static void merge(int[] arr, int left, int mid, int right) {
    int n1 = mid -left + 1;
    int n2 = right - mid;
    int[] L = new int[n1];
    int[] R = new int[n2];
    
    for(int i = 0; i < n1; ++i) L[i] = arr[left - i];
    for(int j = 0; j < n2; ++j) R[j] = arr[mid + 1 + j];
    
    int i = 0, j = 0, k = left;
    while(i < n1 && j < n2) {
        arr[k++] = (L[i] <= R[j]) ? L[i++] : R[j++];
    }
    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];
}
```

***

### 6. 힙 정렬(Heap Sort)

* **개념** : 최대 힙을 구성 후 루트와 교환하며 정렬
* **시간 복잡도** : 항상 $$O(n log n)$$
* **공간 복잡도** : $$O(1)$$
* **특징** : 불안정 정렬, 메모리 사용 적고 최악의 경우에도 안정된 성능.

```java
public static void heapSort(int[] arr) {
    int n = arr.length;
    for(int i = n /2 - 1; i >= 0; i--) heapify(arr, n, i);
    for(int i = n - 1; i > 0; i--) {
        int temp = arr[0];
        arr[0] = arr[i];
        arr[i] = temp;
        heapify(arr, i , 0);
    }
}

private static void heapify(int[] arr, int n, int i) {
    int largest = i;
    int left = 2 * i + 1;
    int right = 2 * i + 2;
    
    if(left < n && arr[left] > arr[largest]) largest = left;
    if(right < n && arr[right] > arr[largest]) largest = right;
    
    if(largest != i) {
        int temp = arr[i];
        arr[i] = arr[largest];
        arr[largest] = temp;
        heapigfy(arr, n, largest);
    }
}
```

***

## 정렬 알고리즘 성능 비교표

| 알고리즘  | 최선         | 평균         | 최악         | 공간복잡도    | 안정성 |
| ----- | ---------- | ---------- | ---------- | -------- | --- |
| 버블 정렬 | O(n)       | O(n^2)     | O(n^2)     | O(1)     | 안정  |
| 선택 정렬 | O(n^2)     | O(n^2)     | O(n^2)     | O(1)     | 불안정 |
| 삽입 정렬 | O(n)       | O(n^2)     | O(n^2)     | O(1)     | 안정  |
| 퀵 정렬  | O(n log n) | O(n log n) | O(n^2)     | O(log n) | 불안정 |
| 병합 정렬 | O(n log n) | O(n log n) | O(n log n) | O(n)     | 안정  |
| 힙 정렬  | O(n log n) | O(n log n) | O(n log n) | O(1)     | 불안정 |

## 정렬 알고리즘 선택 팁

* **입력이 거의 정렬된 경우**: 삽입 정렬
* **일반적인 대용량 데이터**: 퀵 정렬
* **최악의 경우 성능 보장**: 병합 정렬, 힙 정렬
* **안정성이 필요한 경우**: 병합 정렬
* **메모리가 제한된 환경**: 힙 정렬

