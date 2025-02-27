# 배열(Array)

## Java 의 배열 (Array)

### 1. 기본적인 개념과 선언 방법&#x20;

**배열(Array)** 은 같은 타입의 여러 값을 하나의 자료구조로 **순차적으로 저장** 할 수 있는 **객체** 임. 자바에서 배열의 모든 원소(`element`) 는 **동일한 타입** 을 가져야 하며, 배열 생성 시에 결정된 **길이(length )** 를 속성으로 가지게 됨. 배열은 **0부터 시작하는 인덱스** 로 각 원소에 접근하며, 유효 범위를 벗어난 인덱스로 접근하면 `ArrayIndexOutOfBoundsException` 예외가 발생함.

배열 변수를 선언하는 문법은 두 가지 형태가 있음. 예를 들어 정수형 배열의 선언은 `int[] numbers` 형태로 할 수도 있고 `int numbers[]` 처럼 대괄호를 변수 이름 뒤에 붙이는 것도 가능함.(두 형태는 동일한 의미임) 그러나 **배열 변수를 선언** 하는 것만으로는 실제 배열이 생성되지 않으며, 반드시 `new` 연산자를 사용하여 **배열 객체를 생성** 해야함. 배열을 생성할 때는 **크기** 를 지정해야 하며, 한 번 결정된 배열의 크기는 변경할 수 없음.

배열은 **기본형 타입** 과 **참조형 타입** 에 대해 모두 만들 수 있음. 예를 들어 `int[] intArray = new int[5];` 는 5개의 정수 값을 담을 수 있는 배열을 생성하며, `String[] strArray = new String[3];` 는 3개의 문자열(`String`) 을 담을 수 있는 배열을 생성함. 이때 기본형 배열의 요소는 생성과 동시에 해당 타입의 **기본값** 으로 초기화됨. 예를 들어 숫자형 배열 요소는 0(또는 0.0), `char` 배열 요소는 `\u000` (null 문자), `boolean` 배열 요소는 `false` 로 초기화 됨. 반면 **참조형(객체) 배열** 의 요소들은 기본적으로 `null` 로 초기화 됨.&#x20;

즉, 객체 타입의 배열을 생성한 직후에는 각 원소가 아직 어떤 객체도 가리키지 않는 `null` 인 상태임. 아래 코드 예시는 배열의 선언과 생성, 그리고 초기 값을 확인하는 방법을 보여줌.

```java
int[] nums = new int[3]; // 길이가 3인 int 배열 생성(기본 값으로 초기화)
System.out.println(nums.length); // result : 3
System.out.println(nums[0]); // result : 0 (int 배열의 기본값 0)

nums[0] = 10;
nums[1] = 20;
nums[2] = 30;
System.out.println(Arrays.toString(nums)); // result : [10, 20, 30]

String[] names = {"Alice", "Bob", "Charlie"}; // 선언과 동시에 초기화(길이 3)
System.out.println(names.length); // result : 3
System.out.println(names[1]); // result : Bob
names[1] = "David";
System.out.println(Arrays.toString(names)); // result : [Alice, David, Charlie]
```

위 코드에서 `int[] nums = new int[3];`  처럼 **배열 선언과 생성을 한 문장** 으로 작성할 수도 있고,&#x20;

`String[] names = {"Alice", "Bob", "Charlie"};` 와 같이 **리터럴 초기화** 를 통해 배열을 바로 채워 넣을 수도 있음. 리터럴로 배열을 초기화할 때는 `new` 를 명시하지 않고 중괄호 `{}` 안에 값을 콤마로 구분하여 나열함. 다만 리터럴을 사용한 배열 초기화는 **선언과 동시에** 인 경우에만 가능하며, 별도로 배열을 생성한 이후에는 `arr = {...};`  형태로는 할 수 없음.

참고로, 배열 자체는 **객체** 이므로 `nums instanceof Object` 와 같은 검사를 하면 참(`true` ) 을 반환하게 됨. 배열 변수는 객체에 대한 **참조(reference)** 를 담기 때문에, 배열 변수를 복사하거나 전달하면 객체의 **주소값** 이 전달된다는 점이 있다.

### 2. 다차원 배열

자바에서 다차원 배열은 사실 "**배열의 배열"** 형태로 구현됨. 즉, 2차원 배열은 배열을 원소로 갖는 배열이며, 3차원 배열은 2차원 배열을 원소로 갖는 배열이라고 볼 수 있음. **2차원 배열** 은 행(`row`) 과 열(`column` ) 의 형태로 생각할 수 있는데, 자바에서는 각 행을 별도의 1차원 배열로 취급함. 따라서 **자바의 다차원 배열은 각 행의 길이가 서로 달라도 되는 가변 길이 배열** 을 만들 수 있음. (행렬과 달리, 반드시 직사각형 형태일 필요가 없음)

예를 들어 `int[][] matrix = new int[3][4];` 라고 선언하면 3x4 크기의 2차원 배열 객체가 생성됨.

`matrix` 는 3개의 행을 가지며, 각 행에는 4개의 정수형 값을 담을 수 있음.

`matrix.length` 는 3을, 그리고 `matrix[0].length` 는 첫 번째 행의 길이인 4를 반환함. 아래는 2차원 배열의 선언과 초기화, 사용 예시임.

```java
// 2차원 배열 선언과 초기화
int[][] scores = {
    {90, 85, 70}, // 0행 : 원소 3개
    {80, 92},     // 1행: 원소 2개 (길이가 다름)
    {76, 81, 95, 60}// 2행: 원소 4개
};

//2차원 배열의 사용
System.out.println("scores[0][1] = " + scores[0][1]); // result : scores[0][1] = 85
System.out.println("행의 개수 = " + scores.length);       // 출력: 행의 개수 = 3
System.out.println("0행의 길이 = " + scores[0].length);   // 출력: 0행의 길이 = 3
System.out.println("1행의 길이 = " + scores[1].length);   // 출력: 1행의 길이 = 2
```

위 예에서 `scores` 는 3행으로 구성된 2차원 배열이고, 각 행의 길이는 서로 다를 수 있다는 것을 보여줌. `scores[0]` 는 `{90, 85, 70}` 인 1차원 배열이고, `scores[1]` 는 `{88, 92}` 인 1차원 배열임. 이러한 구조 때문에 `scores[1][2]` 와 같이 존재하지 않는 원소에 접근하려 하면 당연히 오류가 발생함.(`ArrayIndexOutOfBoundsException` )

**다차원 배열의 선언** 도 다양한 형태가 가능한데, 예를 들어 `int[][] arr2d;` , `int arr2d[][];` , `int[] arr2d[];` 처럼 쓸 수 있으며 모두 2차원 배열을 가리킴. 3차원 배열도 `int[][][] arr3d = new int[2][3][4];` 처럼 선언함. 또한 2차원 배열을 생성할 때 둘째 차원 크기는 생략하고, 이후에 각 행별로 배열을 할당하는 방법도 있음. 예를 들어 `int[][] jagged = new int[3][];` 처럼 하면 행은 3개로 고정되지만 각 행은 아직 할당되지 않은 상태임. 이후 `jagged[0] = new int[5];` , `jagged[1] = new int[2];` 등으로 행마다 다른 크기의 1차원 배열을 할당할 수 있음. 이런 방식으로 **비정형(Jaggeed) 배열** 을 구현할 수 있음.

### 3. ArrayList 와 배열 차이

자바에서는 배열과 유사하게 가변 길이의 목록을 다룰 수 있도록 `java.util.ArrayList` 클래스도 제공함. 배열과 `ArrayList` 는 여러 면에서 차이가 있는데, 중요한 차이점은 다음과 같음.

* **크기 변경 가능 여부** : **배열은 크기가 고정** 되어 한 번 생성하면 길이를 늘리거나 줄일 수 없음. 반면, `ArrayList` **는 동적으로 크기를 변경** 할 수 있는 **가변 배열 리스트** 임. `ArrayList` 는 내부적으로 배열을 사용하여 구현되어 있는데, 저장 용량이 부족해지면 더 큰 새로운 배열을 만들고 기존 배열의 내용을 복사(copy) 하여 확장함. 이러한 동작은 개발자가 직접 하지 않아도 `add()` 등의 메서드를 호출하면 자동으로 처리됨.
* **데이터 타입 지원: 배열은 기본형 타입과 참조형 타입 모두** 저장할 수 있음. 예를 들어 `int[]` 는 `int` 기본형 값을 직접 담지만, `ArrayList<int>` 와 같은 제네릭 타입은 존재하지 않음. `ArrayList` 는 **객체만 저장** 하도록 설계되어 있으므로, 기본형 값을 저장하려면 해당하는 **래퍼(wrapper) 클래스** 를 사용해야함. 예를 들어 정수를 저장하려면 `ArrayList<Integer> list = new ArrayList<>();` 처럼 선언해야함. 제네릭과 오토박싱(auto-boxing) 기능으로 인해 코드 상으로는 기본형을 넣는 것처럼 보이지만, 실제로는 `Integer` 객체로 변환되어 저장됨. 따라서 기본형을 대량으로 다룰 때 `ArrayList` 를 사용하면 박싱/언박싱에 따른 부가 비용이 발생할 수 있음.
* **사용 방법의 편의성** : 배열은 인덱스를 통한 단순한 저장 및 접근만을 제공하는 반면, `ArrayList` 는 컬렉션 프레임워크의 클래스이므로 **다양한 메서드** 를 제공함. 예를 들어 요소 추가는 `list.add(value);` , 요소 제거는 `list.remove(index)` 또는 `list.remove(Object)` 형태로 간단히 호출할 수 있음. 또한 `list.contains(value)` 로 포함 여부를 확인하거나, `list.size()` 로 크기를 얻는 등 유용한 기능들을 갖추고 있음. 반면 **배열은 이러한 메서드가 없고**, 정해진 인덱스 범위 내에서 값을 넣고(`arr[i] = value`) 꺼내는(`value = arr[i]`) 기능만 제공함. 배열에 새로운 요소를 삽입하거나 중간 요소를 삭제하려면 새로운 배열을 만들고 직접 데이터를 복사하는 등의 **수작업** 이 필요함.
* **성능** : 일반적으로 **배열의 원소 접근이 ArrayList 보다 빠르다** 고 알려져 있음. 배열은 단순한 인덱스 연산으로 해당 메모리 위치에 바로 접근하는 반면, `ArrayList` 의 `get(index)` 메서드는 내부적으로 배열에 접근하면서 추가적인 경계 검사 및 메서드 호출 오버헤드가 있기 때문임. 하지만 현대 JVM 최적화로 그 **차이는 미미** 하며, 인덱스를 통한 조회/수정 성능은 배열과 `ArrayList` 모드 거의 **상수 시간 (O(1))** 임. 실제로 배열과 ArrayList의 성능 차이는 **크기가 매우 큰 경우** 나 **복사/확장 동작이 빈번히 발생** 하는 경우에만 두드러질 것임. `ArrayList` 는 내부 배열의 크기를 늘릴 때마다 새로운 배열 할당 및 복사가 이뤄지므로, **빈번한 추가/삽입이 발생하면 약간의 부하** 가 걸림. 반면, 크기가 고정된 배열은 <mark style="background-color:orange;">such reallocation cost</mark> 가 없으므로 요소 추가/삭제가 거의 없고 **고정된 크기의 데이터 집합을 다룰 때는 배열이 더 효율적** 일 수 있음.
* **기능 지원** : 배열은 언어 차원에서 지원되는 기본 기능이고, `ArrayList` 는 자바의 컬렉션 프레임워크 클래스임. `ArrayList` 는 제네릭을 지원하여 타입 안전성을 제공하고, `Collections` 클래스와 함께 정렬(`Collections.sort(list)` ), 검색(`Collections.binarySearch(list, key)`) 등의 유용한 알고리즘을 메서드로 제공함. 배열도 `Arrays` 유틸리티 클래스를 통해 `Arrays.sort(arr)` , `Arrays.binarySearch(arr, key)` 등의 메서드를 사용할 수 있지만, 컬렉션 프레임워크와 연계된 풍부한 기능은 `ArrayList` 쪽이 더 많음.

**언제 배열을 사용하고 언제 ArrayList 를 사용해야 할지** 에 대해 일반적인 가이드라인을 정리하면 다음과 같음.

**처리할 데이터의 개수가 처음부터 고정되어** 있고 크기가 변하지 않는다면 배열이 간단하고 효율적인 선택임.

예를 들어 **배열의 크기를 사전에 알고 있는 경우** 또는 **성능 최적화가 매우 중요한 경우** (예: 게임 루프나 실시간 시스템에서 수천만 개의 숫자를 빠르게 처리해야 하는 상황) 에는 배열이 유리할 수 있음. 반면, **데이터 개수가 동적으로 변하거나** 수시로 **삽입/삭제 등의 변동이 예상** 된다면 `ArrayList` 와 같은 컬렉션을 사용하는 것이 코드 구현 면에서 훨씬 편리하고 유지보수에도 좋음. 특히 리스트의 크기를 미리 알 수 없거나 사용자 입력 등에 따라 달라지는 경우 `ArrayList` 를 쓰는 것이 자연스러움, 요약하면 **"초기에 크기를 알고 변경되지 않는 경우 배열, 크기를 알 수 없거나 변경해야 하는 경우 ArrayList** 로 선택할 수 있음.

> 성능 관점에서 배열과 `ArrayList` 중 무엇을 선택해야 할지 고민될 때는, 먼저 **코드의 명확성과 기능적 요구** 를 기준으로 선택하고 성능은 나중에 프로파일링하여 문제 될 때 최적화 하는 것이 좋음. 성능 차이는 상황에 따라 달라지지만, **빈번한 확장이 없을 경우** 둘 다 비슷한 성능을 보이며, 큰 규모의 데이터 처리에서 병목이 없다면 편의성이 높은 `ArrayList` 를 쓰는 편이 나음. 또한 `ArrayList` 에도 초기 용량을 지정하는 생성자(`new ArrayList<>(initialCapacity)` ) 가 있으므로, 예상 크기를 설정하여 확장 빈도를 줄일 수도 있음.

**배열과 ArrayList 의 비교**

```java
// array 사용
int[] arr = {1,2,3};
System.out.println(arr.length); // result:  3
// arr[3] = 4; // 컴파일 오류 또는 실행 시 예외 - 배열 크기를 넘는 추가는 불가


// ArrayList 사용
ArrayList<Integer> list = new ArrayList<>(Arrays.asList(1,2,3));
System.out.println(list.size()); // result : 3
list.add(4); // 크기 자동 증가, 4추가
System.out.println(list.size()); // result : 4
System.out.println(list); // result [1,2,3,4]
list.remove(Integer.valueOf(2)); // 값이 2인 요소 제거
System.out.println(list); // result [1,3,4]
```

위 코드에서 배열 `arr` 은 `{1,2,3}` 으로 고정되어 더 이상 원소를 추가할 수 없지만, `ArrayList` 인 `list` 는 `add()` 를 통해 크기가 동적으로 늘어나는 것을 볼 수 있음. 또한 `remove` 를 사용하여 특정 값을 쉽게 제거할 수 있음. 이처럼 **직관적인 메서드 지원** 이 `ArrayList` 의 큰 장점임. 다만, 배열도 필요에 따라 `ArrayList.asList(arr)` 등을 통해 리스트로 변환하거나, `List` 를 배열로 변환하는 `list.toArray()` 메서드를 활용하여 두 가지 자료구조를 함께 사용할 수도 있음.

### 4. 배열의 복사 및 조작

#### 배열 복사(Copy)

자바에서 배열을 복사하는 방법으로 몇 가지가 제공됨.

* `System.arraycopy(...)` - JDK에서 제공하는 **저수준 배열 복사** 메서드임.\
  `System.arraycopy(src, srcPos, dest, destPos, length)` 형식으로 사용하며, 원본 배열(`src` ) 의 `srcPos` 위치부터 `length` 만큼을 대상 배열(`dest` ) 의 `destPos` 위치로 복사함. 이 메서드는 네이티브(native) 구현으로 빠르게 부분 복사도 가능하기 때문에 효율적으로 사용할 수 있음.
* `Array.copyOf` / `Arrays.copyOfRange` - `java.util.Arrays` 유틸리티 클래스에서 제공하는 정적 메서드들로, 내부적으로 `System.arraycopy` 를 사용하여 새로운 배열을 생성하고 복사까지 한번에 수행함. `Arrays.copyOf(원본, 새로운 길이)` 를 호출하면 지정한 길이의 새로운 배열을 생성한 뒤, 원본 배열을 처음부터 그 길이만큼 복사하여 반환함. (원본보다 큰 길이를 지정하면 나머지 요소들은 타입의 기본값으로 채워짐) `Arrays.copyOfRange(원본배열, 시작인덱스, 끝 인덱스)` 는 원본배열의 일부 구간을 복사하여 새로운 배열을 만들어줌.
* `clone`  **메서드** - 모든 배열은 `Ojbect` 의 `clone()` 메서드를 구현하고 있으며, 이를 호출하면 **해당 배열 객체의 얕은 복사(shallow copy)** 를 수행함. 즉, 동일한 내용의 새로운 배열 객체를 반환함. 예를 들어 `int[] a = {1,2,3}; int[] b = a.clone();` 을 하면 `b` 는 `a`  와 같은 `[1,2,3]` 내용을 가진 별도의 `int[]` 객체가 됨. (주의 : 객체 배열의 경우 요소의 객체까지 복제하지는 않고 **객체의 참조 값만 복사** 한다는 점에서 얕은 복사임)

**배열 복사 방법**

```java
int[] original = {10, 20, 30, 40};

// 1. clone() 을 이용한 복사
int[] copy1 = original.clone();
copy1[0] = 99;
System.out.println(Arrays.toString(original)); // 출력: [10, 20, 30, 40] (원본 영향 없음)
System.out.println(Arrays.toString(copy1));    // 출력: [99, 20, 30, 40] (별도 배열)

// 2. Arrays.copyOf 를 이용한 복사
int[] copy2 = Arrays.copyOf(original, original.length);
int[] copy3 = Arrays.copyOf(original, 2);  // 앞의 2개 요소만 복사
System.out.println(Arrays.toString(copy2)); // 출력: [10, 20, 30, 40]
System.out.println(Arrays.toString(copy3)); // 출력: [10, 20]

// 3. System.arraycopy 를 이용한 복사
int[] dest = new int[5];
System.arraycopy(original, 1, dest, 0, 3);
// original의 인덱스 1부터 3개 값을 dest의 인덱스 0부터 복사
System.out.println(Arrays.toString(dest));   // 출력: [20, 30, 40, 0, 0]

```

위 코드에서 `clone()` 이나 `Arrays.copyOf` 를 사용하면 새 배열 객체를 얻기 때문에 원본 배열을 수정해도 복사본에는 영향을 주지 않음. `System.arraycopy` 를 사용하면 부분 복사도 가능하며, 위 코드에서는 `original` 의 일부를 `dest` 에 복사하여 `[20,30,40,0,0]` 결과를 얻었음. (`dest` 배열이 미리 길이 5로 생성되어 있으므로 남은 부분은 기본값 0 으로 유지됨)

#### 배열에서 요소 추가 및 삭제

배열은 한 번 생성되면 크기를 변경할 수 없음. 그렇지만 새로운 배열을 만들어 기존 데이터를 옮기는 방식으로 **요소를 추가하거나 삭제한 효과** 를 낼 수 있음. 이는 번거롭기 때문에 보통 **ArrayList** 를 사용하지만, 배열만으로 구현하려면 다음과 같은 방법을 씀.

* **요소 추가** : 기존 배열보다 길이가 1 큰 새 배열을 만들고, 기존 배열 내용을 복사한 다음 새 요소를 넣음. `Array.copyOf` 를 활용하면 편리함.

```java
int[] arr = {5,6,7};
int newValue = 8;
// 배열 길이를 1 늘려서 복사본 생성
int[] newArr = Arrays.copyOf(arr, arr.length + 1);
newArr[newArr.length - 1] = newValue; // 마지막 위치에 새 값 추가
System.out.println(Arrays.toString(newArr)); // result : [5,6,7,8]
```

* **요소 삭제** : 삭제하고자 하는 인덱스를 제외한 새로운 배열을 만들고, 기존 배열에서 해당 부분을 건너뛰고 복사함. 이를 위해 `System.arraycopy` 를 사용하면 편리함.

```java
int[] value = {5,6,7,8};
int removeIndex = 2; // 인덱스 2의 값(7) 을 제거한다고 가정

int[] smaller = new int[values.length - 1];
//removeIndex 이전의 요소들을 복사
System.arraycopy(values, 0, smaller, 0, removeIndex);
//removeIndex 이후의 요소들을 복사
System.arraycopy(values, removeIndex + 1, smaller, removeIndex, values.length - removeIndex - 1);
System.out.println(Arrays.toString(smaller)); // result : [5,6,8]
```

위 코드에서 `values` 배열에서 인덱스 2의 요소 `7` 을 제거하기 위해, 길이가 하나 작은 새 배열 `smaller` 를 만들고 두 번의 `System.arraycopy` 로 제거 대상 앞부분과 뒷부분을 복사했음. 결과적으로 `smaller` 에는 `[5,6,8]` 처럼 원하는 요소가 제거된 배열이 만들어짐. 이처럼 배열에서 는 요소 추가/삭제 시 직접 새로운 배열을 만들고 복사해야 하므로 다소 번거로움. 이런 이유로 **다량의 삽입/삭제 작업에는 ArrayList** 등의 컬렉션을 주로 사용함.

#### 배열의 정렬 및 검색

자바는 배열을 위한 유틸리티 메서드를 `java.util.Arrays` 클래스에서 제공함. **정렬(sort)** 과 **이진 검색(binary search)** 이 대표적임.

* **정렬** : `Arrays.sort(array)` 메서드를 사용하면 배열을 **오름차순으로 제자리 정렬** 함. 기본형 배열이나 `Comparable` 을 구현한 객체 배열은 별도의 비교자 없이 자연 순서대로 정렬됨. 또한 `Arrays.sort(array, Comparator)` 형태로 비교자를 제공하여 사용자 정의 순서로 정렬할 수도 있음.

```java
int[] numbers = {3,1,4,1,5};
Arrays.sort(numbers);
System.out.println(Arrays.toString(numbers));
// result : [1,1,3,4,5] (오름차순 정렬 완료)
```

* **이진 검색** : `Arrays.binarySearch(array, key)` 메서드는 **정렬된 배열** 에서 특정 값(`key`) 을 **이진 탐색** 으로 찾아 그 인덱스를 반환함. 이진 탐색은 매우 빠른 검색 (O(log n)) 을 제공하지만, **배열이 정렬되어 있어야 제대로 된 결과를 얻을 수 있음.** 만약 정렬되지 않은 배열에 이 메서드를 사용하면 결과는 의미가 없으며 정의되지 않음.&#x20;

`Arrays.binarySearch` 의 반환값은 찾는 값이 **존재하면 해당 인덱스** 를, **없으면 음수 값** 을 반환하는데, 이 음수 값에는 삽입 위치에 대한 정보가 담겨 있음. JDK 문서에 따르면 반환값이 음수일 경우 그것을 `-(insertion point) -1` 라는 공식으로 해석할 수 있음. 간단히 말해, 해당 값이 배열에 없다면 그 값이 들어갈 위치의 인덱스를 음수로 변환하여 돌려준다는 뜻임. 예를 들어 위에서 정렬된 `numbers` 배열에서 `Arrays.binarySearch(numbers, 4)` 는 `4` 의 인덱스인 `3` 을 반환하지만, `Arrays.binarySearch(numbers, 2)` 처럼 배열에 없는 값을 검색하면 `-3` 을 반환함. `-3` 은 삽입 위치가 인덱스 2임을 의미함. (`numbers` 배열에서 인덱스 2 자리에 `2` 를 넣으면 정렬 순서가 유지됨.)



아래는 정렬과 이진 검색을 함께 보여주는 예시임.

```java
String[] fruits = {"Orange", "Apple", "Banana", "Grape"};
Arrays.sort(fruits);
System.out.println(Arrays.toString(fruits));
// 출력: [Apple, Banana, Grape, Orange]

int index = Arrays.binarySearch(fruits, "Grape");
System.out.println("Grape의 인덱스: " + index);       
// 출력: Grape의 인덱스: 2   (정렬된 후의 인덱스)

int index2 = Arrays.binarySearch(fruits, "Pear");
System.out.println("Pear의 검색 결과: " + index2);   
// 출력: Pear의 검색 결과: -4  (음수 결과는 삽입 위치 정보)
```

위 예시는 문자열 배열 `fruits` 를 정렬한 후 "Grape" 를 이진 검색하여 인덱스 `2`  를 얻었음. "Pear" 은 배열에 없기 때문에 음수 값이 반환되었는데, `-4` 라는 값은 만약 "Pear" 를 삽입한다면 인덱스 3(`Orange` 앞, 배열 끝 위치) 에 들어가게 될 것이라는 뜻임. 이처럼 `binarySearch` 의 음수 반환값은 단순이 "-1" 이 아니라 **삽입점에 대한 정보를 포함** 한다는 것을 유의해야함.



마지막으로, 배열을 정렬하는 다른 방법이나 검색하는 다른 방법으로 **직접 구현** (예 : 버블 정렬, 선형 검색 등) 할 수도 있지만, 자바가 제공하는 `Arrays.sort` 는 고성능의 정렬 알고리즘을 사용하고 있으며, `binarySearch` 역시 매우 효율적임. 따라서 면접이나 알고리즘 학습 목적이 아니라면 이러한 내장 기능을 적극 활용하는 것이 좋음. 또한 `Arrays` 클래스에는 배열을 다루는데 유용한 메서드들이 많으므로 (예 : `Arrays.toString, Arrays,equals, Arrays.fill` 등) 필요할 때 문서를 참고하여 사용하면 편리함.

