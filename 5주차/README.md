# 6~7주차. 클래스 심화, 구조체, 튜플 및 배열

<br>

## 1. 클래스 심화 (읽기 전용 필드, 분할 클래스, 확장 메소드)

- **클래스 간의 사용자 정의 형식 변환** (`implicit`, `explicit`)
  - 일반적으로 서로 상속 관계가 아닌 완전히 다른 클래스(예: `Yen` 클래스와 `Won` 클래스)의 객체끼리는 대입 연산자(`=`)를 통해 값을 넣을 수 없다.
  - 하지만 금액(통화), 거리(km, mile), 무게 등 서로 **단위 변환(환율 적용 등)**이 필요한 경우, 프로그래머가 직접 형변환 연산자를 오버로딩하여 두 객체 사이의 변환 방식을 정의할 수 있다.

  - 연산자 오버로딩과 마찬가지로 반드시 `public static`으로 선언해야 한다.

- `implicit` 연산자 (암시적 형변환 허용)
  - 컴파일러가 알아서 형변환을 수행하도록 허용하는 연산자이다.
  - 캐스팅 연산자 `()`를 생략하는 **암시적 형변환**과, 캐스팅 연산자를 붙이는 **명시적 형변환 모두를 지원**한다.
  - 데이터 손실의 위험이 없고, 변환 로직이 명확할 때 사용하면 코드가 간결해진다.

```csharp
public class Yen : Currency
{
    public Yen(decimal money) : base(money) { }

    // Yen 객체를 Won 객체로 변환하는 implicit 연산자 정의
    static public implicit operator Won(Yen yen)
    {
        // 환율(13배)을 적용하여 새로운 Won 객체를 만들어 반환
        return new Won(yen.Money * 13m);
    }
}

static void Main()
{
    Yen yen = new Yen(100);

    Won won1 = yen;       // 암시적 형변환 가능 (에러 없음)
    Won won2 = (Won)yen;  // 명시적 형변환도 당연히 가능
}
```

- `explicit` 연산자 (명시적 형변환 강제)
  - 개발자가 실수로 다른 타입의 객체를 대입하는 것을 막기 위해, **반드시 캐스팅 연산자 `()`를 통해서만 변환되도록 강제**하는 연산자이다.
  - 암시적으로 대입(`=`)하려고 하면 컴파일 에러를 발생시킨다.
  - 변환 과정에서 데이터 손실이 발생할 수 있거나(예: 소수점 버림), 개발자에게 "지금 형변환이 일어나고 있다"는 사실을 명확히 인지시켜야 할 때 사용한다.

```csharp
public class Dollar : Currency
{
    public Dollar(decimal money) : base(money) { }

    // Dollar 객체를 Won 객체로 변환하되, explicit(명시적)으로만 허용함
    static public explicit operator Won(Dollar dollar)
    {
        // 환율(1000배)을 적용하여 새로운 Won 객체를 만들어 반환
        return new Won(dollar.Money * 1000m);
    }
}

static void Main()
{
    Dollar dollar = new Dollar(1);

    // Won won3 = dollar; // 컴파일 에러 발생! (암시적 대입 불가)
    Won won4 = (Won)dollar; // 개발자가 의도적으로 (Won)을 붙여 명시적 형변환을 해야만 성공
}
```

- **읽기 전용 필드 (`readonly`)**
  - 클래스의 멤버 변수 선언 시 `readonly` 키워드를 붙이면, 값이 한 번 정해진 후에는 변경할 수 없다.
  - **`const`와의 차이점**: `const` 상수는 변수 선언 시에만 초기화할 수 있지만, `readonly` 필드는 객체가 생성될 때 **생성자 내부에서도 초기화가 가능**하므로 동적으로 값을 지정하고 고정할 때 유리하다.

```csharp
class Configuration
{
    private readonly int min;
    private readonly int max;

    public Configuration(int v1, int v2)
    {
        min = v1;
        max = v2;
    }

    public void ChangeMax(int newMax)
    {
        // max = newMax; // 컴파일 에러
    }
}
```

- 중첩 클래스 (Nested Class)
  클래스 내부에 또 다른 클래스를 선언하는 문법이다.
  - 객체를 생성하고 메소드를 호출하는 기본적인 방법은 일반 클래스와 동일하다.
  - 가장 큰 차이점이자 강력한 장점은, **자신을 감싸고 있는 외부 클래스(Outer Class)의 멤버에 자유롭게 접근**할 수 있다는 점이다.
  - 심지어 외부 클래스의 **`private` 멤버에도 접근이 가능**하다.
  - 외부에 노출할 필요가 전혀 없고, 특정 클래스 내부에서만 데이터 관리용으로 밀접하게 사용되는 도우미(Helper) 객체를 만들 때 사용한다. 중첩 클래스 자체를 `private`으로 선언하면 외부 세계로부터 완벽하게 감출 수 있다.

```csharp
class OuterClass
{
    private int OuterMember; // 외부 클래스의 private 필드

    // 중첩 클래스 선언
    class NestedClass
    {
        public void DoSomething()
        {
            OuterClass outer = new OuterClass();

            // 핵심: 중첩 클래스 내부에서는 외부 클래스의 private 멤버에 접근하여 조작할 수 있음!
            outer.OuterMember = 10;
        }
    }
}

class Configuration
{
    // 클래스 내부에서만 사용할 목적으로 private 중첩 클래스 생성
    private class ItemValue
    {
        private string item;
        private string value;

        public void SetValue(Configuration config, string item, string value)
        {
            // 외부 클래스인 Configuration의 private 필드(listConfig)에 자유롭게 접근
            // config.listConfig.Add(this);
        }
    }
}
```

- **분할 클래스 (`partial class`)**
  - 클래스의 구현 코드가 너무 길어질 때, 여러 파일이나 블록에 나누어 구현할 수 있게 해주는 기능이다. 컴파일 시에는 컴파일러가 이를 하나의 클래스로 합쳐서 빌드한다.
- **확장 메소드 (Extension Method)**
  - 기존 클래스의 소스 코드를 건드리지 않고, 외부에서 새로운 메소드를 추가하는 마법 같은 기능이다 (심지어 `int`, `string` 같은 기본 자료형에도 추가 가능).
  - **선언 방법**: 반드시 **정적(`static`) 클래스** 안에 **정적(`static`) 메소드**로 선언해야 하며, 첫 번째 매개변수 앞에 **`this` 키워드와 함께 확장할 대상 타입**을 지정한다.

```csharp
// 1. 분할 클래스 선언
partial class MyClass { public void Method1() { } }
partial class MyClass { public void Method2() { } } // 컴파일 시 Method1과 합쳐짐

// 2. 확장 메소드를 담을 정적 클래스
public static class IntegerExtension
{
    // int 형식에 Square() 메소드를 확장
    public static int Square(this int myInt)
    {
        return myInt * myInt;
    }
}

static void Main()
{
    int a = 3;
    Console.WriteLine(a.Square()); // 출력: 9 (기본 int형에 Square 기능이 추가됨!)
}
```

<br>

## 2. 구조체 (`struct`)

클래스와 매우 비슷하게 데이터와 메소드를 가질 수 있지만, 메모리 동작 방식이 완전히 다른 데이터 형식이다.

- **구조체와 클래스의 결정적 차이**
  - **클래스**: 참조 형식(Reference Type)으로 힙(Heap) 영역에 메모리가 할당되며, 가비지 컬렉터(GC)에 의해 관리된다. 객체 복사 시 얕은 복사(주소만 복사)가 일어난다.
  - **구조체**: **값 형식(Value Type)**으로 스택(Stack) 영역에 할당되며, 블록이 끝나면 즉시 소멸하여 GC의 부담을 덜어준다. 객체 간 대입 연산 시 **깊은 복사(값 전체가 통째로 복사됨)**가 일어난다.
- **구조체 생성 규칙**
  - 매개변수가 없는 기본 생성자는 선언할 수 없다.
  - 생성자 내부에서 반드시 구조체의 **모든 필드를 초기화**해야만 에러가 나지 않는다.
  - `new` 연산자를 사용하지 않고 선언만으로도 사용할 수 있으나, 이 경우 필드를 사용하기 전에 직접 모든 값을 할당해야 한다.
- **변경 불가능 구조체 (`readonly struct`)**
  - 구조체 선언 시 `readonly struct`로 선언하면 상태를 절대 변경할 수 없다. 내부의 모든 필드 역시 반드시 `readonly`로 선언해야 한다.

```csharp
// 1. 일반 구조체 선언
struct Point3D
{
    public int X, Y, Z;

    // 매개변수 있는 생성자만 허용되며, 모든 필드를 반드시 초기화해야 함
    public Point3D(int X, int Y, int Z) { this.X = X; this.Y = Y; this.Z = Z; }
}

// 2. 변경 불가능 구조체 선언
readonly struct RGBColor
{
    public readonly byte R; // 필드도 반드시 readonly
    public RGBColor(byte r) { R = r; }
}

static void Main()
{
    Point3D p1 = new Point3D(10, 20, 30);
    Point3D p2 = p1; // 값 형식인 구조체의 대입 (깊은 복사 발생)

    p2.Z = 400; // p2를 수정해도 p1에는 영향이 없음!
    Console.WriteLine(p1.Z); // 출력: 30
}
```

<br>

## 3. 튜플 (Tuple)

프로그램 전체에서 쓸 형식 선언 없이, 즉석에서 여러 필드를 묶어 사용할 수 있는 값 형식의 복합 데이터 구조이다. 값 형식이므로 스택에서 바로 소멸하여 메모리 부담이 적다.

- **명명되지 않은 튜플 (Unnamed Tuple)** : 필드 이름을 지정하지 않고 괄호와 값만으로 만든다 (`Item1`, `Item2`로 접근).
- **명명된 튜플 (Named Tuple)** : `(필드명: 값)` 형태로 이름을 직접 지정하여 생성한다.
- **튜플 분해 (Deconstruction)** : 튜플 안의 값들을 여러 지역 변수에 한 번에 쪼개어 담을 수 있으며, 필요 없는 필드는 무시(`_`)할 수 있다.
- **레코드(`record`)와의 차이점**: 튜플은 이름이 없고 즉석에서 데이터를 묶는 용도(값 형식)지만, 레코드는 명확한 이름을 가지며 주로 객체 간 전송에 쓰는 불변 객체(참조 형식)이다.

```csharp
static void Main()
{
    // 1. 명명되지 않은 튜플
    var tuple1 = (123, 789);
    Console.WriteLine($"{tuple1.Item1}, {tuple1.Item2}"); // 기본 프로퍼티인 Item1, Item2로 접근

    // 2. 명명된 튜플
    var tuple2 = (Name: "홍길동", Age: 17);
    Console.WriteLine($"{tuple2.Name}, {tuple2.Age}");

    // 3. 튜플 분해 (특정 필드 무시)
    var (name, _) = tuple2; // Age 필드는 무시(버림)
    Console.WriteLine($"{name}"); // 출력: 홍길동
}
```

- 튜플의 위치 패턴 매칭 (Positional Pattern Matching)
  - 튜플은 `switch` 문이나 `switch` 식(Expression)의 분기 조건으로 매우 유용하게 활용할 수 있다.
  * 튜플이 분해된 요소의 **위치**에 따라 값이 일치하는지 판단하여 분기한다.
  * 특정 자리에 어떤 값이 오든 상관없을 때는 무시 연산자(`_`)를 사용하고, `when` 절을 결합해 세밀한 추가 조건까지 한 번에 검사할 수 있다.

```csharp
static void Main(string[] args)
{
    // 직업과 나이를 담은 명명된 튜플 생성
    var alice = (job: "학생", age: 17);
    var bob = (job: "학생", age: 23);
    var charlie = (job: "일반", age: 15);

    // 튜플을 반환받아 switch 식에서 곧바로 분기 처리
    Console.WriteLine($"alice : {GetDiscountRate(alice)}"); // 출력: alice : 0.2
    Console.WriteLine($"bob : {GetDiscountRate(bob)}");     // 출력: bob : 0.1
    Console.WriteLine($"charlie : {GetDiscountRate(charlie)}"); // 출력: charlie : 0.1
}

// 매개변수로 튜플(object 형식으로 업캐스팅)을 받는 메소드
private static double GetDiscountRate(object client)
{
    // 튜플의 각 자리 위치(Positional)에 따른 패턴 매칭 수행
    return client switch
    {
        // 1번째 자리가 "학생"이고, 2번째 자리 변수 n이 18 미만일 때
        ("학생", int n) when n < 18 => 0.2,

        // 1번째 자리가 "학생"이고, 2번째 자리는 아무 값(_)이나 올 때 (위 조건에 안 걸린 18세 이상)
        ("학생", _) => 0.1,

        // 1번째 자리가 "일반"이고, 2번째 자리 변수 n이 18 미만일 때
        ("일반", int n) when n < 18 => 0.1,

        // 1번째 자리가 "일반"이고, 2번째 자리는 아무 값(_)이나 올 때
        ("일반", _) => 0.05,

        // 그 외의 모든 경우 (디폴트)
        _ => 0,
    };
}
```

<br>

## 4. 1차원 배열 (Array)과 인덱싱 (`^`)

동일한 데이터 형식의 묶음으로 0번부터 시작하는 인덱스를 갖는다.

- C# 8.0부터 **`^` 연산자(System.Index)**를 사용해 배열의 맨 끝에서부터 역순으로 접근할 수 있다.
  - `^1`은 배열의 가장 마지막 원소, `^2`는 마지막에서 두 번째 원소를 의미한다 (주의: `^0`은 배열 길이를 나타내므로 접근 시 인덱스 에러 발생).
- **배열 초기화의 3가지 방법**

```csharp
static void Main()
{
    // 초기화 방법 1: 용량 명시 + 값 블록
    string[] arr1 = new string[3] { "A", "B", "C" };

    // 초기화 방법 2: 용량 생략 (값의 개수로 자동 지정)
    string[] arr2 = new string[] { "A", "B", "C" };

    // 초기화 방법 3: new 연산자 및 형식 모두 생략 (가장 축약된 형태)
    string[] arr3 = { "A", "B", "C" };

    // ^ 연산자를 이용한 역방향 인덱싱
    Console.WriteLine(arr3[^1]); // 배열의 맨 마지막 요소(C) 출력
}
```

<br>

## 5. System.Array 클래스의 주요 멤버와 배열 분할 (`..`)

C#의 모든 배열은 .NET의 CTS(Common Type System)에 따라 `System.Array` 클래스를 상속받으므로, 배열을 다루기 위한 매우 강력하고 다양한 내장 기능을 기본적으로 제공한다.

### System.Array 의 주요 메소드와 프로퍼티

- **정적(Static) 메소드**
  - `Sort()` : 배열 요소를 정렬한다.
  - `BinarySearch<T>()` : 이진 탐색을 수행하여 요소의 인덱스를 찾는다.
  - `IndexOf()` : 특정 데이터의 인덱스를 반환한다.
  - `TrueForAll()` : 배열의 모든 요소가 지정한 조건(Predicate)에 부합하는지 여부(`bool`)를 반환한다.
  - `FindIndex<T>()` : 배열에서 지정한 조건에 부합하는 **첫 번째 요소의 인덱스**를 반환한다.
  - `Resize<T>()` : 배열의 크기를 재조정한다.
  - `Clear()` : 배열의 모든 요소를 초기화한다.
  - `ForEach<T>()` : 배열의 모든 요소에 대해 동일한 작업(Action)을 일괄적으로 수행한다.
  - `Copy<T>()` : 배열의 일부를 다른 배열에 복사한다.
- **인스턴스 메소드 및 프로퍼티**
  - `GetLength()` : 배열에서 지정한 차원의 길이를 반환한다 (인스턴스 메소드).
  - `Length` : 배열의 전체 길이를 반환한다 (프로퍼티).
  - `Rank` : 배열의 차원을 반환한다 (프로퍼티).

```csharp
using System;

class Program
{
    // ForEach와 TrueForAll 등에서 조건/동작으로 사용할 도우미 메소드
    private static void Print(int value) { Console.Write($"{value} "); }
    private static bool CheckPassed(int score) { return score >= 60; }

    static void Main()
    {
        int[] scores = new int[] { 80, 74, 81, 90, 34 };

        // 1. ForEach : 모든 요소에 대해 Print 메소드 실행
        Array.ForEach<int>(scores, new Action<int>(Print));
        Console.WriteLine();

        // 2. Sort : 배열 오름차순 정렬
        Array.Sort(scores);

        // 3. Rank, BinarySearch, IndexOf
        Console.WriteLine($"차원(Rank): {scores.Rank}");
        Console.WriteLine($"81의 위치: {Array.BinarySearch<int>(scores, 81)}");
        Console.WriteLine($"90의 위치: {Array.IndexOf(scores, 90)}");

        // 4. TrueForAll : 배열의 *모든* 요소가 60 이상(CheckPassed)인지 검사
        Console.WriteLine($"모두 통과?: {Array.TrueForAll<int>(scores, CheckPassed)}");

        // 5. FindIndex : 60 미만인 첫 번째 요소의 인덱스를 찾음 (람다식 활용)
        int index = Array.FindIndex<int>(scores, new Predicate<int>((score) => score < 60));
        scores[index] = 61; // 찾은 요소(34)의 값을 61로 수정

        // 6. GetLength, Resize, Length
        Console.WriteLine($"기존 배열 길이: {scores.GetLength(0)}");
        Array.Resize<int>(ref scores, 10); // 배열 크기를 10으로 늘림 (빈 공간은 0으로 채워짐)
        Console.WriteLine($"늘어난 배열 길이: {scores.Length}");

        // 7. Copy : scores 배열의 0번 인덱스부터 3개의 요소를 sliced 배열의 0번부터 복사
        int[] sliced = new int[3];
        Array.Copy(scores, 0, sliced, 0, 3);
        Array.ForEach<int>(sliced, new Action<int>(Print)); // 출력: 61 74 80
    }
}
```

<br>

### 배열 분할 (`..` 연산자 / System.Range)

- C# 8.0부터 도입된 `System.Range` 형식으로, `시작인덱스 .. 끝인덱스` 형식을 사용해 배열의 일부를 간결하게 슬라이싱할 수 있다.
- **특징** : 시작 인덱스는 분할 결과에 포함되지만, **끝 인덱스의 값은 제외**된다.
- 시작이나 끝을 생략하면 각각 처음(`0`)과 끝(`^0` - 배열의 끝)으로 간주된다.

```csharp
static void Main()
{
    int[] scores = { 80, 74, 81, 90, 34 };

    // Range 객체를 명시적으로 생성하여 사용
    System.Range r1 = 0..3;
    int[] sliced1 = scores[r1]; // { 80, 74, 81 } 추출

    // 직접 대괄호 안에 연산자 입력 (가장 자주 쓰이는 방식)
    int[] sliced2 = scores[0..3]; // 인덱스 0부터 2까지 추출
    int[] sliced3 = scores[..3];  // 처음부터 2까지 (시작 인덱스 생략)
    int[] sliced4 = scores[1..];  // 인덱스 1부터 끝까지 (끝 인덱스 생략)
    int[] sliced5 = scores[..];   // 배열 전체

    // System.Index 객체(^)와 결합
    int[] sliced6 = scores[..^1]; // 처음부터 마지막 요소 '전'까지 추출
}
```

<br>

## 6. 다차원 배열 (2차원, 3차원)

1차원이 여러 개 모인 2차원 이상의 배열 형태이다.

- **2차원 배열 선언** : 콤마(`,`)를 사용하여 차원을 늘린다 (`데이터형식[,] 배열명 = new 데이터형식[세로, 가로]`).
- **3차원 이상 선언** : 콤마를 추가하여 선언한다 (`int[,,] arr = new int[4, 3, 10];`).

```csharp
static void Main()
{
    // 1. 2차원 배열 선언과 접근
    int[,] arr2D = new int[2, 3]; // 2행 3열의 2차원 배열
    arr2D[0, 0] = 1;

    // 2. 2차원 배열 초기화 (초기화 블록을 계층적으로 작성)
    int[,] arr2D_init = new int[2, 3] {
        { 1, 2, 3 },
        { 4, 5, 6 }
    };
}
```
