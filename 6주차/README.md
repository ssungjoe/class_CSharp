# 가변 배열과 컬렉션 (Collections)

## 1. 가변 배열 (Jagged Array)

다양한 길이의 배열을 요소로 갖는 다차원 배열이다. 일반적인 2차원 배열과 달리, 가변 배열의 요소로 입력되는 각 배열은 그 길이가 모두 같을 필요가 없다.

- **선언 형식**: `데이터형식[][] 배열이름 = new 데이터형식[가변 배열의 용량][];`
- **할당 및 초기화**:
  - 메인 배열의 용량만 먼저 지정한 뒤, 각 인덱스 요소에 길이가 다른 1차원 배열을 개별적으로 생성하여 할당한다.
  - 가변 배열의 선언과 동시에 초기화 블록을 사용하여 하위 배열들을 한 번에 할당할 수도 있다.

```csharp
// 1. 선언 및 개별 할당
int[][] arr = new int[5][]; // 배열의 용량을 5로 지정
arr[0] = new int[10];       // 0번 요소에는 길이가 10인 배열 생성
arr[1] = new int[9];        // 1번 요소에는 길이가 9인 배열 생성

// 2. 선언 및 개별 초기화
int[][] jagged = new int[3][];
jagged[0] = new int[5] { 1, 2, 3, 4, 5 };
jagged[1] = new int[] { 10, 20, 30 };

// 3. 선언과 동시에 초기화
int[][] jagged2 = new int[2][] {
    new int[] { 1000, 2000 },
    new int[4] { 6, 7, 8, 9 }
};
```

<br>

## 2. 컬렉션 (Collection) 개요

같은 성격을 띤 데이터의 모음을 담는 자료구조이다.
<br>배열 또한 .NET이 제공하는 다양한 컬렉션 자료구조의 일부이며, 컬렉션들은 `ICollection` 인터페이스를 상속받는다. 주요 제공 자료구조로는 `ArrayList`, `Queue`, `Stack`, `Hashtable`이 있다.

<br>

## 3. ArrayList

가장 **배열과 닮은 컬렉션**으로, 배열과 달리 필요에 따라 자동으로 용량이 늘어나거나 줄어드는 동적 배열이다.

- **특징 및 한계 (박싱/언박싱 오버헤드)**
  - 컬렉션의 요소에 접근할 때는 배열처럼 `[]` 연산자를 이용한다.
  - `Add()`, `Insert()` 등의 메소드는 `object` 형식의 매개변수를 사용하므로 모든 데이터 형식을 담을 수 있다.

  - 단, 값 형식의 데이터를 다룰 때 내부적으로 **박싱(Boxing)과 언박싱(Unboxing)**이 발생하여 적지 않은 성능 오버헤드를 요구한다 (이를 해결하기 위해 C#은 일반화 컬렉션을 별도로 제공함).

- **주요 메소드**
  - `Add()` : **가장 마지막에 있는 요소 뒤**에 새 요소를 추가한다.
  - `RemoveAt()` : 특정 인덱스에 있는 요소를 제거한다.
  - `Insert()` : 원하는 위치(인덱스)에 새 요소를 삽입한다.

```csharp
using System;
using System.Collections;

class Program
{
    static void Main()
    {
        ArrayList list = new ArrayList();
        list.Add(10); // [10]
        list.Add(20); // [10, 20]
        list.Add(30); // [10, 20, 30]

        list.RemoveAt(1);   // 1번 인덱스(20) 삭제 -> [10, 30]
        list.Insert(1, 25); // 1번 인덱스에 25 삽입 -> [10, 25, 30]

        // 전혀 다른 데이터 형식(문자열) 삽입
        list.Add("abc"); // 에러 없이 들어감 -> [10, 25, 30, "abc"]
        list.Add("def"); // -> [10, 25, 30, "abc", "def"]

        // 꺼낼 때는 무조건 object 타입으로 꺼내야 함
        foreach (object obj in list)
        {
            Console.Write($"{obj} "); // 출력: 10 25 30 abc def
        }
    }
}
```

- 모든 데이터형식을 object 형식의 매개변수로 입력받음

```csharp
public virtual int Add(object? value);
public virtual void Insert(int index, object? value);
```

<br>

## 4. Queue (큐)

입력은 오직 뒤에서, 출력은 앞에서만 이루어지는 **선입선출(First In First Out)** 방식의 자료구조이다.

- **활용**: OS에서 CPU가 처리해야 할 작업을 대기열로 정리할 때, 혹은 프린터가 여러 문서를 순서대로 처리할 때 사용한다.
- **주요 메소드**
  - `Enqueue()` : 가장 마지막에 있는 항목 뒤에 새 항목을 추가한다.
  - `Dequeue()` : 제일 앞에 있던 항목을 출력하고 컬렉션에서 제거한다. (반환 타입이 `object`이므로 캐스팅 필요)

```csharp
Queue que = new Queue();
que.Enqueue(1);
que.Enqueue(2);

int a = (int)que.Dequeue(); // 가장 먼저 들어간 1이 반환됨
```

<br>

## 5. Stack (스택)

Queue와 반대로 먼저 들어온 데이터가 나중에 출력되고, 나중에 들어온 데이터가 먼저 출력되는 **후입선출(Last In First Out)** 방식의 자료구조이다.

- **주요 메소드**
  - `Push()` : 데이터를 컬렉션의 가장 위에 쌓아 올린다.
  - `Pop()` : 제일 위에 쌓여 있는 데이터를 꺼내서 반환하며, 해당 데이터는 컬렉션에서 제거된다. 그 아래 있던 데이터가 새로운 최상위 데이터가 된다.

```csharp
Stack stack = new Stack();
stack.Push(1); // 최상위 1
stack.Push(2); // 최상위 2

int a = (int)stack.Pop(); // 가장 나중에 들어간 2가 반환되고, 1이 다시 최상위가 됨
```

<br>

## 6. Hashtable (해시테이블)

키(key)와 값(value)의 쌍으로 이루어진 데이터를 다룰 때 사용하는 컬렉션이다.

- **특징**: **해싱(Hashing)**을 통해 원하는 데이터를 찾을 때 키를 이용해 데이터가 저장된 컬렉션 내 주소를 단번에 계산해 낸다. 이로 인해 탐색 속도가 매우 빠르며 (배열의 인덱스 접근 속도에 준함), `int`, `float`, `string` 등 어떤 형식이든 키로 사용할 수 있다.

```csharp
Hashtable ht = new Hashtable();
ht["book"] = "책";
ht["cook"] = "요리사";

Console.WriteLine(ht["book"]); // 출력값: 책
```

<br>

## 7. 컬렉션 초기화 (Collection Initialization)

배열이나 컬렉션 생성자를 이용하여 컬렉션을 손쉽게 초기화할 수 있다.

- **배열을 이용한 초기화** : `ArrayList`, `Queue`, `Stack`은 객체를 생성할 때 생성자의 매개변수로 기존 배열 객체를 넘겨주면, 그 배열의 데이터를 바탕으로 내부 데이터를 채운다.
- **컬렉션 초기자 사용** : `ArrayList`는 생성자 뒤에 `{ }` 기호를 사용하여 컬렉션 요소의 목록을 직접 입력할 수 있다.
- **Hashtable 초기화** : 딕셔너리 초기자(`[key] = value`)나 컬렉션 초기자(`{key, value}`) 방식을 모두 사용할 수 있다.

```csharp
int[] arr = { 123, 456, 789 };

// 1. 배열을 매개변수로 넘겨서 초기화
ArrayList list = new ArrayList(arr); // 123, 456, 789
Stack stack = new Stack(arr);        // Pop() 시 789, 456, 123 순으로 나옴
Queue queue = new Queue(arr);        // Dequeue() 시 123, 456, 789 순으로 나옴

// 2. 컬렉션 초기자 사용
ArrayList list2 = new ArrayList() { 11, 22, 33 };

// 3. Hashtable 딕셔너리 초기자
Hashtable ht1 = new Hashtable() {
    ["하나"] = 1,
    ["둘"] = 2
};

// 4. Hashtable 컬렉션 초기자
Hashtable ht2 = new Hashtable() {
    { "하나", 1 },
    { "둘", 2 }
};
```

<br><br>

# 인덱서, foreach의 원리, 인터페이스의 모든 것

## 1\. 인덱서 (Indexer)

객체 내의 데이터에 배열처럼 인덱스(`[]`)를 이용해서 접근하게 해주는 특수한 프로퍼티다.

- **쉬운 설명**: 배열은 `arr[0]`처럼 대괄호를 써서 값을 꺼낸다. 하지만 우리가 직접 만든 클래스의 객체는 배열이 아니므로 `obj[0]`처럼 쓸 수 없다. **"내가 만든 객체에도 배열 대괄호(`[]`)를 달아주고 싶을 때"** 사용하는 문법이 바로 인덱서다.
- **특징**: 프로퍼티처럼 식별자(이름)를 따로 가지지 않고, `this[형식 index]` 키워드를 사용하여 선언한다.

<!-- end list -->

```csharp
class MyList
{
    private int[] array;
    public MyList() { array = new int[3]; }

    // 인덱서 선언: 객체를 배열처럼 쓰게 해주는 마법의 문법
    public int this[int index]
    {
        get { return array[index]; } // 인덱스를 이용해 내부 데이터 반환
        set
        {
            // 방이 꽉 찼으면 배열 크기를 동적으로 늘려주는 기능 포함
            if (index >= array.Length) {
                Array.Resize<int>(ref array, index + 1);
                Console.WriteLine($"Array Resized: {array.Length}");
            }
            array[index] = value; // 내부 데이터 저장
        }
    }
    public int Length { get { return array.Length; } }
}

static void Main()
{
    MyList list = new MyList();
    list[0] = 0; // 인덱서 덕분에 내가 만든 객체(list)에 대괄호를 쓸 수 있다!
    list[4] = 4; // 범위를 초과하면 내부에서 알아서 늘린다 (Array Resized: 5 출력)
}
```

<br>

## 2\. `foreach` 문이 가능한 객체 만들기

`foreach` 문은 인덱스 변수, 세미콜론, 증감식이 필요 없어 편리하지만, 아무 객체나 사용할 수 있는 것은 아니다.

- **쉬운 설명**: `foreach`를 돌리려면 객체 내부에 "**순서대로 다음 데이터를 넘겨주는 기능**"이 반드시 있어야 한다.
- **기본 조건**: `foreach` 문은 **`IEnumerable`** 인터페이스를 상속하는 형식만 지원하며, 이 인터페이스는 `IEnumerator` 객체를 반환하는 `GetEnumerator()` 메소드를 가진다.
- **`IEnumerator`의 필수 구현 멤버**:
  1.  `MoveNext()`: 다음 요소로 이동한다 (성공 시 `true`, 끝이면 `false` 반환).
  2.  `Reset()`: 컬렉션의 첫 번째 위치 '앞(-1번)'으로 되돌아간다.
  3.  `Current` (프로퍼티): 컬렉션의 현재 요소를 반환한다.

### 2-1. `yield` 키워드를 이용한 간편한 구현

위의 `IEnumerator` 멤버들을 일일이 구현하기는 번거롭다. C\#은 `yield` 키워드를 통해 컴파일러가 이 복잡한 코드를 알아서 생성하게 해준다.

- **쉬운 설명**: `yield return`은 호출자에게 값을 하나 툭 던져주고 그 자리에서 **일시 정지(Pause)**한다. <br>다음 바퀴 때 호출되면 처음부터가 아니라, 일시 정지됐던 바로 그 지점부터 실행을 복구한다. `yield break`는 반복을 완전히 끝내버린다.

<!-- end list -->

```csharp
class MyEnumerator
{
    int[] numbers = { 1, 2, 3, 4 };

    // IEnumerable이 요구하는 메소드
    public IEnumerator GetEnumerator()
    {
        yield return numbers[0]; // 1을 던져주고 여기서 일시 정지!
        yield return numbers[1]; // 다음 바퀴 때 깨어나서 2를 던지고 정지!
        yield return numbers[2];
        yield break;             // 여기서 GetEnumerator() 종료 (아래 코드는 무시됨)
        yield return numbers[3];
    }
}

static void Main()
{
    var obj = new MyEnumerator();
    foreach (int i in obj) Console.WriteLine(i); // 출력: 1, 2, 3
}
```

### 2-2. `foreach`의 진짜 원리: `IEnumerator` 직접 구현

`foreach` 문은 아무 객체나 막 돌려주는 것이 아니라, 객체 내부에 "**데이터를 순서대로 넘겨주는 반복기(가이드)**"가 있어야만 작동한다.
이 반복기 역할을 하려면 클래스가 `IEnumerable`과 `IEnumerator` 인터페이스를 상속받고, 아래의 4가지 기능을 반드시 직접 만들어줘야 한다.

#### 핵심 역할

- **`GetEnumerator()`**: "우리 데이터 구경할 가이드(반복기) 한 명 배정해 주세요!" 하고 요청하는 메소드다. 보통 자기 자신(`this`)을 반환한다.
- **`MoveNext()`**: 가이드에게 "다음 칸으로 한 발짝 이동해!"라고 명령하는 기능이다. 이동에 성공하면 `true`를 반환하고, 끝에 도달해서 더 이상 이동할 곳이 없으면 `false`를 반환하여 `foreach`를 끝낸다.
- **`Reset()`**: 가이드의 위치를 컬렉션의 첫 번째 데이터 '앞(출발선)'으로 되돌려 놓는다. (보통 인덱스를 `-1`로 맞춘다).
- **`Current`**: 가이드에게 "지금 밟고 있는 칸에 있는 데이터 내놔!" 하고 값을 받아오는 프로퍼티다.

#### 왜 출발선(인덱스)이 `0`이 아니라 `-1`일까?

이것이 가장 헷갈리는 부분이다. `foreach` 루프가 시작되면, 데이터를 달라고(`Current`) 하기 전에 **무조건 `MoveNext()`를 먼저 한 번 실행**한다.
따라서 시작 위치가 `0`이면 첫 번째 `MoveNext()` 호출 시 `1`번 인덱스로 넘어가 버려 첫 데이터를 놓치게 된다. 그래서 `Reset()` 상태의 위치를 `-1`로 맞추어 두어야, 첫 `MoveNext()` 때 `0`번 인덱스로 정상 진입하게 된다.

```csharp
using System;
using System.Collections;

// foreach를 쓰려면 IEnumerable과 IEnumerator 둘 다 상속받아야 한다.
class MyList : IEnumerator, IEnumerable
{
    private int[] array;
    int position = -1; // 현재 가이드의 위치 (0번 인덱스 앞인 출발선 -1)

    public MyList() { array = new int[3]; }

    // 인덱서 (배열처럼 쓰기 위해)
    public int this[int index]
    {
        get { return array[index]; }
        set
        {
            if (index >= array.Length) Array.Resize<int>(ref array, index + 1);
            array[index] = value;
        }
    }

    // --- 1. IEnumerator 멤버 필수 구현 ---

    // 다음 위치로 이동
    public bool MoveNext()
    {
        // 현재 위치가 배열의 맨 끝에 도달했다면?
        if (position == array.Length - 1)
        {
            Reset(); // 다음 foreach 사용을 위해 위치를 다시 출발선(-1)으로 리셋
            return false; // 더 이상 읽을 데이터가 없으므로 false 반환 (foreach 종료)
        }

        position++; // 위치를 한 칸 앞으로 전진
        return (position < array.Length); // 안전 범위 내에 있으면 true 반환하여 반복 계속
    }

    // 첫 위치 앞(출발선)으로 이동
    public void Reset()
    {
        position = -1;
    }

    // 현재 위치의 데이터를 반환
    public object Current
    {
        get { return array[position]; }
    }


    // --- 2. IEnumerable 멤버 필수 구현 ---

    // foreach문이 가장 먼저 호출하여 가이드(반복기)를 요구하는 메소드
    public IEnumerator GetEnumerator()
    {
        // 이 클래스(this) 안에 MoveNext, Reset, Current가 다 있으니 자기 자신을 던져줌
        return this;
    }
}

class Program
{
    static void Main()
    {
        MyList list = new MyList();
        for (int i = 0; i < 5; i++) list[i] = i; // 인덱서를 통해 데이터 입력

        // 우리가 직접 만든 MyList 객체지만, 인터페이스 규격을 완벽히 맞췄으므로 foreach 사용 가능!
        foreach (int e in list)
        {
            Console.WriteLine(e); // 출력: 0 1 2 3 4
        }
    }
}
```

이처럼 `IEnumerator`를 직접 구현하는 방식은 코드가 길어지지만, `foreach`가 내부적으로 어떻게 한 칸씩 이동하고 끝을 파악하는지 명확하게 이해할 수 있는 근본적인 뼈대가 된다.

<br>

## 3\. 인터페이스 (Interface) 기초

클래스가 반드시 구현해야 할 메소드나 프로퍼티의 '규격(약속)'만을 정의한 껍데기다.

- **쉬운 설명**: USB 포트의 모양(규격)만 정해둔 것과 같다. 이 규격만 맞추면 키보드를 꽂든, 마우스를 꽂든 컴퓨터는 알아서 작동시킨다.
- **특징**:
  - `interface` 키워드를 사용한다.
  - 메소드, 이벤트, 인덱서, 프로퍼티의 **구현부가 없다**.
  - 접근 제한자(예: `private`)를 사용할 수 없으며 무조건 공개된다.
  - 인스턴스(객체)를 만들 수 없지만, 파생 클래스 객체의 위치를 저장하는 **참조(변수 형태)로는 쓸 수 있다**.

<!-- end list -->

```csharp
// 약속(규격) 정의: "이걸 상속받으면 무조건 WriteLog를 만들어야 함!"
interface ILogger { void WriteLog(string message); }

// 약속을 지키는 클래스 1 (콘솔에 출력)
class ConsoleLogger : ILogger
{
    public void WriteLog(string msg) { Console.WriteLine(msg); }
}

static void Main()
{
    // 인터페이스 자체의 객체는 못 만들지만, 인터페이스 타입 변수에 자식 객체를 담을 수는 있다.
    ILogger logger = new ConsoleLogger();
    logger.WriteLog("Hello World");
}
```

<br>

## 4\. 인터페이스의 다중 상속과 다형성

### 4-1. 다중 상속 지원

C\# 클래스는 여러 부모를 상속받을 경우 어떤 부모의 메소드를 써야 할지 충돌하는 **"죽음의 다이아몬드"** 문제가 발생하므로 다중 상속이 금지되어 있다.
하지만 **인터페이스는 내용물이 없는 외형(껍데기)이므로 충돌할 일이 없어 다중 상속이 허용**된다.

- **명시적 인터페이스 구현**: 인터페이스의 메소드를 구현할 때는 반드시 `public`을 명시해야 한다.
  <br>하지만 메소드 이름 앞에 인터페이스명을 직접 붙이면(`IMonitor.TurnOn()`) `public`을 생략할 수 있다. 이 경우 객체 변수로는 직접 호출할 수 없고, **반드시 해당 인터페이스로 형변환(`as`)해야만 호출 가능**하다.

<!-- end list -->

```csharp
class Computer { }
interface IMonitor { void TurnOn(); }
interface IKeyboard { }

// 클래스 하나와 여러 인터페이스를 한꺼번에 다중 상속
class Notebook : Computer, IMonitor, IKeyboard
{
    // 인터페이스명을 직접 붙이는 '명시적 구현' (public 생략)
    void IMonitor.TurnOn() { Console.WriteLine("모니터 켜짐"); }
}

static void Main()
{
    Notebook notebook = new Notebook();
    // notebook.TurnOn(); // 컴파일 오류! (Notebook 객체로는 직접 호출 불가)

    // 반드시 IMonitor 인터페이스로 형변환해야 호출 가능
    IMonitor mon = notebook as IMonitor;
    mon.TurnOn();
}
```

### 4-2. 자동 다형성 적용

인터페이스의 메소드는 C\# 컴파일러가 알아서 **가상 메소드(`virtual`)로 간주**한다. 따라서 자식 클래스에서 구현할 때 `virtual`이나 `override` 키워드를 사용할 필요가 없으며, 자동으로 다형성이 적용된다.
<br>
오히려 사용하면 에러가 발생한다.

<br>

## 5\. 빈 인터페이스 활용 (마커 인터페이스)

메소드가 하나도 없는 비어 있는 인터페이스를 만들어 클래스에 상속시키면, 그 자체로 특정한 **'의미(꼬리표)'를 부여**할 수 있다.

- **쉬운 설명**: "이 인터페이스를 상속받은 애들은 특별 관리 대상이야\!"라고 표시해 두고, 나중에 `is` 연산자로 꼬리표가 붙어 있는지 검사하는 용도다.

<!-- end list -->

```csharp
// ToString을 재정의한 클래스에만 붙일 빈 꼬리표(인터페이스)
interface IObjectToString { }

class Person : IObjectToString  // 꼬리표 부착!
{
    string name = "홍길동";
    public override string ToString() { return "Person: " + this.name; }
}

static void Main()
{
    object obj = new Person();

    // 이 객체에 꼬리표(IObjectToString)가 달려있는지 검사
    if (obj is IObjectToString) {
        Console.WriteLine(obj.ToString()); // 출력: Person: 홍길동
    }
}
```

<br>

## 6\. 콜백 (Callback) 기능

인터페이스의 메소드를 상속된 클래스에서 반드시 구현해야 한다는 점을 이용해, 코드를 호출한 쪽에서 다시 내 코드를 부르게(Call-back) 만드는 기법이다.

- **쉬운 설명**: 기본적으로 오름차순 정렬만 하는 `Array.Sort` 메소드에, 내가 만든 내림차순 정렬 규칙(`IComparer` 인터페이스 구현체)을 넘겨주면, `Sort`가 내부적으로 내 규칙(메소드)을 다시 '콜백'하여 내림차순으로 정렬을 수행한다.

<!-- end list -->

```csharp
// IComparer 인터페이스를 상속받아 비교 규칙(콜백용 메소드) 정의
class IntegerCompare : IComparer
{
    // Array.Sort 내에서 이 메소드를 '콜백'하여 두 숫자를 비교함
    public int Compare(object x, object y)
    {
        int xValue = (int)x;
        int yValue = (int)y;

        if (xValue > yValue) return -1; // 내림차순이 되도록 -1 반환
        else if (xValue == yValue) return 0;
        return 1;
    }
}

static void Main()
{
    int[] intArray = { 1, 2, 3, 4, 5 };
    // 내림차순 규칙이 담긴 객체를 넘겨줌 (콜백 연결)
    Array.Sort(intArray, new IntegerCompare());

    foreach(int item in intArray) Console.Write(item + ", "); // 출력: 5, 4, 3, 2, 1,
}
```

<br>

## 7\. 느슨한 결합 (Loose Coupling)

클래스 간에 강하게 결합(Tight coupling)되어 있으면 나중에 다른 부품으로 교체할 때 코드를 대대적으로 수정해야 하는 약점이 있다. 인터페이스를 매개변수로 활용하면 이를 '느슨한 결합'으로 바꾸어 유연성을 극대화할 수 있다.

- **쉬운 설명**: 스위치를 만들 때 '컴퓨터 전용'으로 만들면 나중에 모니터를 연결할 수 없다. 대신 \*\*"전원 규격(`IPower`)이 맞는 기계면 다 받아줄게\!"\*\*라고 만들면, 기계가 컴퓨터든 모니터든 스위치 코드는 수정할 필요가 없다.

<!-- end list -->

```csharp
interface IPower { void TurnOn(); }

class Computer : IPower { public void TurnOn() { Console.WriteLine("Computer 켜짐"); } }
class Monitor : IPower { public void TurnOn() { Console.WriteLine("Monitor 켜짐"); } }

class Switch
{
    // Computer나 Monitor 클래스를 직접 명시하지 않고, 인터페이스 규격(IPower)을 받음
    public void PowerOn(IPower machine)
    {
        machine.TurnOn(); // 어떤 기계가 들어오든 느슨하게 연결되어 잘 작동함
    }
}
```

<br>

## 8\. 인터페이스 상속과 기본 구현 메소드

### 8-1. 인터페이스를 상속하는 인터페이스

클래스처럼 인터페이스도 다른 인터페이스를 상속받아 기능을 확장할 수 있다 (`interface 파생 : 부모`).

- **사용 이유**: 다른 사람이 만든 어셈블리(dll) 형태의 인터페이스를 고칠 수 없거나, 기존 인터페이스를 이미 너무 많은 클래스가 사용하고 있어서 원본을 수정하면 대형 오류가 날 때 새 인터페이스로 상속하여 쓴다.

### 8-2. 인터페이스의 기본 구현 메소드

원래 인터페이스는 구현부(내용물)가 없어야 하지만, C\# 8.0부터는 예외적으로 **기본 구현 코드를 가지는 메소드**를 인터페이스 내부에 만들 수 있다.

- **쉬운 설명**: 나중에 새로운 룰(`WriteError`)을 추가했을 때, 옛날에 만들어둔 클래스들이 이 룰을 구현 안 했다고 에러를 내뿜으면 곤란하다. 그래서 인터페이스 쪽에 "너희가 안 만들면 그냥 이 '기본 동작'으로 퉁칠게"라고 배려해 주는 기능이다.
- **주의점**: 이 기본 구현 메소드는 자식 클래스의 객체 변수로는 접근할 수 없으며, **반드시 해당 인터페이스 참조로 업캐스팅했을 때만 사용 가능**하다.

<!-- end list -->

```csharp
interface ILogger
{
    void WriteLog(string message); // 필수 구현 약속

    // 내용물이 들어있는 '기본 구현 메소드'
    void WriteError(string error) { WriteLog(error); }
}

class ConsoleLogger : ILogger
{
    public void WriteLog(string message) { Console.WriteLine(message); }
    // WriteError 메소드를 구현하지 않아도 컴파일 에러가 발생하지 않음!
}

static void Main()
{
    ConsoleLogger clogger = new ConsoleLogger();
    // clogger.WriteError("Fail"); // 컴파일 에러 발생! (클래스 객체로는 접근 불가)

    ILogger logger = new ConsoleLogger(); // 인터페이스로 형변환(업캐스팅)
    logger.WriteError("System Fail"); // 기본 구현 메소드 정상 작동
}
```
