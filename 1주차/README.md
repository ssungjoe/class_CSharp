# 1~2주차. C# 프로그래밍 시작 및 데이터 타입

<br>

## 1. C#과 .NET 플랫폼의 이해

C#은 마이크로소프트의 앤더스 헤일스버그(Anders Hejlsberg)가 개발한 객체지향 언어로, .NET Framework에 최적화되어 있으며 윈도, 맥, 리눅스 등 다양한 운영체제(크로스 플랫폼)에서 동작한다.

- **.NET 플랫폼 (Platform)**
  - 운영체제(OS)의 차이에 따른 응용프로그램 실행 환경 문제를 해결해 주는 소프트웨어 플랫폼이다.
- **CLR (Common Language Runtime) (중요)**
  - C# 프로그램은 운영체제와 직접 소통하지 않고 .NET 라이브러리와 함께 설치되는 가상 실행 환경인 CLR 위에서 동작한다.
  - **JIT (Just In Time) 컴파일**: C# 소스코드는 바로 기계어로 바뀌지 않고 중간 언어(IL, Intermediate Language) 형태의 EXE 파일로 빌드된다. 프로세스가 실행되는 시점(Just In Time)에 CLR이 메모리에 적재되어 이 IL 코드를 운영체제가 이해할 수 있는 네이티브 코드로 변환한다.
    <br>
    - 장점 : 플랫폼에 최적화된 코드 생성, 단점 : 실행시 컴파일 부담
  - **가비지 컬렉션 (Garbage Collection)**: 프로그래머가 메모리를 직접 해제하지 않아도 CLR의 가비지 컬렉터가 사용하지 않는 메모리를 자동으로 회수하여 관리해 준다.
- **.NET 호환 언어 규약**
  - **CTS (Common Type System)** : C#, VB.NET 등 .NET 호환 언어들이 공통으로 따르는 데이터 형식 표준 체계이다.
  - **CLS (Common Language Specification)** : .NET 호환 언어가 지켜야 할 최소한의 공용 언어 사양이다.

<br>

## 2. 라이브러리와 프레임워크

- **.NET 라이브러리** : 응용프로그램 개발을 위해 미리 정의된 클래스나 네임스페이스의 집합이다.
  - **네임스페이스 (Namespace)** : 용도나 분야별로 비슷한 코드를 묶어둔 논리적 단위 (예: 입출력을 위한 `System`, 윈도 폼을 위한 `System.Windows.Forms`).
- **프레임워크 (Framework)** : 제어 역전(IoC: Inversion of Control)이 일어나는 대규모 라이브러리 템플릿이다.
  - **제어 역전 (IoC)** : 프로그램의 초기화부터 종료까지의 흐름을 사용자 코드가 아닌 프레임워크가 주도적으로 관리하며, 버튼 클릭 같은 이벤트가 발생할 때만 사용자 코드를 호출하는 방식이다.

<br>

## 3. C# 프로그램의 기본 구조와 골격

C++과 유사하지만 객체지향적 특성이 강제되어 모든 코드는 반드시 클래스 내부에 존재해야 한다.

- **`using System;`** : C++의 `#include`와 유사하게, `Console` 클래스 등이 포함된 `System` 네임스페이스를 사용하겠다고 컴파일러에 알린다.
  - 다른 네임스페이스에서 `Program` 클래스 쓰는법 : `using FirstProgram` 또는 `FirstProgram.Program` 사용
- **`class`** : C# 프로그램을 구성하는 기본 단위이며 데이터와 메소드를 담는 틀이다.
- **`static void Main(string[] args)` (진입점)** : 프로그램이 시작되는 진입점(Entry Point)으로, **반드시 단 1개만 존재**해야 한다.
  - `static` 한정자로 인해 객체 생성 없이도 프로그램 구동 시 메모리에 즉시 할당되어 CLR이 호출할 수 있다.

```csharp
using System; // System 네임스페이스 사용 선언

namespace FirstProgram // 네임스페이스 선언
{
    class Program // 클래스 선언
    {
        // 프로그램의 진입점 (Entry Point)
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!"); // 화면에 출력하고 줄 바꿈
        }
    }
}
```

<br>

## 4. 기본 용어와 식별자 규칙

- **표현식과 문장**
  - 표현식(Expression): `273`, `10 + 20`처럼 값을 만들어내는 코드.
  - 문장(Statement): 실행 가능한 최소 단위로 반드시 세미콜론(`;`)으로 끝난다.
- **키워드 (일반 vs 문맥)**
  - C#이 미리 의미를 부여한 단어로, 변수로 쓸 수 없는 **일반 키워드**(`bool`, `break`, `class` 등)와 특정 문맥에서만 동작하는 **문맥 키워드**(`add`, `async`, `alias` 등)로 나뉜다.
- **식별자 (Identifiers) 생성 규칙과 `@` 기호**
  - 숫자로 시작할 수 없고, 공백을 포함할 수 없다.
  - 기본적으로 키워드를 이름으로 쓸 수 없으나, **키워드 앞에 `@`를 붙이면 식별자로 강제 사용이 가능**하다.

```csharp
static void Main(string[] args)
{
    // 식별자 규칙 예외 처리 (키워드를 변수명으로 사용)
    int @class = 10;
    Console.WriteLine(@class);
}
```

<br>

## 5. 값 형식 (Value Type)과 참조 형식 (Reference Type) (핵심 개념)

C#의 데이터 타입은 총 15가지 기본 데이터 형식(숫자, 논리, 문자열, 오브젝트)과 복합 데이터 형식(구조체, 클래스, 배열)으로 나뉘며, 메모리 할당 방식에 따라 두 가지로 구분된다.

- **값 형식 (Value Type)**
  - 기본 데이터 형식(숫자, 논리)과 구조체가 이에 해당한다.
  - 변수가 실제 데이터를 직접 담으며, **스택(Stack) 메모리** 영역에 할당된다.
  - 변수가 선언된 코드 블록 `{}`이 끝나면 스택에서 늦게 생성된 변수부터 차례대로 **자동으로 즉시 제거**된다.
- **참조 형식 (Reference Type)**
  - 문자열(`string`), 클래스, 배열, `object`가 이에 해당한다.
  - 실제 데이터는 **힙(Heap) 메모리** 영역에 저장되고, 스택 메모리에는 그 데이터가 있는 '주소(참조값)'만 할당된다.
  - 코드 블록이 끝나 스택의 주소가 지워져도 힙 영역의 실제 데이터는 남아있으며, 이후 CLR의 **가비지 컬렉터(GC)**가 불필요하다고 판단할 때 스스로 메모리를 회수한다.

```csharp
static void Main(string[] args)
{
    // 값 형식 (스택에 5가 직접 저장됨)
    int n1 = 5;
    int n2 = n1;

    // 참조 형식 (힙에 "C#"이 저장되고, txt1과 txt2는 스택에서 힙의 동일한 주소를 가리킴)
    string txt1 = "C#";
    string txt2 = txt1;
}
```

<br>

## 6. C#의 기본 데이터 형식 (숫자형)과 오버플로

- **정수형 (총 9가지)**
  - 부호 있는 정수는 `sbyte`, `short`, `int`, `long`이며, 부호 없는 정수는 앞에 `u`를 붙인 `ushort`, `uint`, `ulong`이다.
  - **주의사항**: C/C++과 달리 C#에서 `byte`는 **부호 없는 정수 (0~255)**를 의미하며, 부호 있는 1바이트 정수는 `sbyte` (-128~127)를 사용해야 한다.
  - **음수 표현 (2의 보수법)**: `sbyte` 형에서 음수는 2의 보수법(비트를 반전시키고 1을 더함)을 통해 저장된다.
- **진수 리터럴 접두사**: 2진수는 `0b` (`0b1111_0000`), 16진수는 `0x` (`0xF0`) 접두사를 사용한다.
- **오버플로(Overflow)와 언더플로(Underflow)**: 변수가 허용하는 데이터 형식의 최대/최소 범위를 넘어가면 값이 순환해버려 예상치 못한 값이 출력된다.

```csharp
static void Main(string[] args)
{
    // 1. byte와 sbyte 형 변환 (2의 보수 확인)
    byte a = 255;
    sbyte b = (sbyte)a;
    Console.WriteLine(b); // 출력: -1 (255가 sbyte 범위를 넘어 2의 보수로 해석됨)

    // 2. 오버플로 (Overflow)
    uint max_uint = uint.MaxValue; // 4294967295
    max_uint = max_uint + 1;
    Console.WriteLine(max_uint); // 출력: 0 (비트가 순환되어 모두 0이 됨)

    // 3. 언더플로 (Underflow)
    int x = -30;
    uint y = (uint)x; // 부호 없는 정수에 음수 강제 대입
    Console.WriteLine(y); // 출력: 4294967266
}
```

<br>

## 7. 부동 소수점 (실수형) 및 문자, 논리 형식

- **부동 소수점 형식 (IEEE 754)** : 소수점이 고정되어 있지 않은 실수형 자료형으로 정밀도에 따라 3가지로 나뉘며, 리터럴 대입 시 **반드시 접미사**를 붙여야 한다.
  - `float` (4바이트, 유효숫자 7자리): 부호 비트(1) + 지수부(8) + 가수부(23)로 구성되며 접미사 `f` 필수.
  - `double` (8바이트, 유효숫자 15~16자리): 접미사 `d` (생략 시 기본적으로 double로 취급).
  - **`decimal` (16바이트, 유효숫자 29자리)**: 접미사 `m`. 성능보다는 금융, 회계 등 극한의 정밀도가 필요한 계산에 쓰이는 특별한 자료형이다.
- **문자 (`char`)와 논리 (`bool`)**
  - `char`는 16비트 정수 계열이지만 문자 데이터를 다루며 사칙연산 시 컴파일 에러가 발생한다.
  - `bool`은 오직 `true`, `false`만 저장한다.

```csharp
static void Main(string[] args)
{
    // 숫자 사이의 '_'는 가독성을 위한 자릿수 구분자로 무시됨

    float f_val = 3.1415_9265_3589_7932_3846_2643_3832f; // f 접미사 필수
    double d_val = 3.1415_9265_3589_7932_3846_2643_3832; // 접미사 생략 가능
    decimal m_val = 3.1415_9265_3589_7932_3846_2643_3832m; // m 접미사 필수 (초고정밀도)

    Console.WriteLine(f_val); // 3.1415927
    Console.WriteLine(d_val); // 3.141592653589793
    Console.WriteLine(m_val); // 3.1415926535897932384626433832
}
```

<br>

## 8. object 형식과 박싱(Boxing) / 언박싱(Unboxing)

- **`object` 형식** : C#의 **모든 데이터 형식은 `object` 형식으로부터 상속**받는다. 따라서 `object` 변수에는 숫자, 논리, 문자열 등 어떤 데이터든 담을 수 있다.
- **박싱 (Boxing)** : 스택에 있는 값 형식(`int`, `float` 등)을 `object` 변수에 대입하면, CLR이 이 값을 힙(Heap) 영역으로 포장해서 복사해 올리고 스택에는 그 주소를 할당하는 과정이다.
- **언박싱 (Unboxing)** : 박싱되어 힙에 저장된 값을 다시 꺼내 값 형식 변수에 저장하는 과정이다.

```csharp
static void Main(string[] args)
{
    // 어떤 데이터든 object에 담을 수 있음
    object a = 123;         // int 박싱
    object c = true;        // bool 박싱
    object d = "안녕하세요."; // string (참조 형식은 박싱 없이 그대로 주소 참조)

    // 박싱과 언박싱 과정
    object boxed_val = 20;            // 박싱: 스택의 20이 힙 메모리로 복사됨
    int unboxed_val = (int)boxed_val; // 언박싱: 힙의 값을 다시 스택으로 꺼내옴
}
```

<br>

## 9. 데이터 형 변환 (Type Casting)

- **암시적 변환** : 범위가 작은 데이터 형식에서 큰 형식으로 변환할 때(예: `byte` -> `short`) 컴파일러가 부가 코드 없이 자동 수행한다.
- **명시적 변환** : 개발자가 괄호 `()`를 사용해 강제로 형변환을 지시한다.
  - 실수형 간의 변환(`float` <-> `double`)은 2진수와 10진수를 오가는 계산 과정에서 정밀도 손상이 발생할 수 있다.
  - 실수를 정수로 명시적 변환하면 소수점 이하는 모두 버림 처리된다.

```csharp
static void Main(string[] args)
{
    int x = 128; // sbyte의 최대값 127보다 1 큰 수
    Console.WriteLine(x);  // 128

    sbyte y = (sbyte)x; // 명시적 형변환 후 오버플로 발생
    Console.WriteLine(y);  // -128
}
```

- **문자열과 숫자 사이의 변환** : 캐스팅 `()` 연산자로는 컴파일 에러가 발생하므로 전용 메소드를 사용한다.
  - **`Parse()` 메소드** : 문자열을 숫자로 변환한다 (`int.Parse`, `float.Parse`).
  - **`ToString()` 메소드** : 숫자를 문자열로 변환한다 (`object`로부터 상속받은 기능을 재정의한 것).

```csharp
static void Main(string[] args)
{
    // 1. 부동 소수점 -> 정수 변환 (소수점 버림)
    float float_val = 0.9f;
    int int_val = (int)float_val;
    Console.WriteLine(int_val); // 출력: 0

    // 2. 숫자 -> 문자열 변환 (ToString)
    int num1 = 123;
    string str1 = num1.ToString(); // "123"

    // 3. 문자열 -> 숫자 변환 (Parse)
    string str2 = "123456";
    int num2 = int.Parse(str2); // 123456
}
```

<br>

## 10. 상수 (const)와 열거 형식 (enum)

데이터가 한 번 할당되면 임의로 값을 변경할 수 없는 형식이다.

- **`const` 상수** : 변수 선언 시 앞에 `const`를 붙이며, 초기화 후 값을 변경하려 하면 컴파일 에러가 발생한다.
- **`enum` 열거 형식** : 여러 상수를 묶어 새로운 데이터 형식을 만든다. 내부 상수에 값을 명시하지 않으면 **첫 번째 요소부터 0부터 차례대로 1씩 증가하여 자동 할당**된다.

```csharp
// 클래스 바깥이나 내부에 enum 선언 (기반 자료형 생략 시 int)
enum DialogResult { YES, NO, CANCEL = 50, CONFIRM, OK }

class Program
{
    static void Main(string[] args)
    {
        // 1. 상수 (const)
        const int a = 3;
        // a = 4; // 상수는 값 변경 시 컴파일 에러 발생

        // 2. 열거 형식 (enum) 사용 및 값 확인
        Console.WriteLine((int)DialogResult.YES);     // 0 (자동 할당)
        Console.WriteLine((int)DialogResult.NO);      // 1 (자동 할당)
        Console.WriteLine((int)DialogResult.CANCEL);  // 50 (수동 할당)
        Console.WriteLine((int)DialogResult.CONFIRM); // 51 (앞선 값 + 1)
        Console.WriteLine((int)DialogResult.OK);      // 52 (앞선 값 + 1)

        // 열거 형식으로 변수 생성
        DialogResult result = DialogResult.YES;
        Console.WriteLine(result == DialogResult.YES); // True
    }
}
```

<br>

## 11. Nullable 형식과 var 키워드

- **Nullable 형식 (`?`)**
  - 본래 값 형식(int, float 등)에는 `null`을 넣을 수 없으나, 자료형 뒤에 `?`를 붙이면 '비어있는 상태(null)'를 가질 수 있는 변수가 된다.
  - **`HasValue` 속성** : 유효한 값을 가지고 있는지 `true`/`false`로 확인한다.
  - **`Value` 속성** : 실제 값을 반환한다. `null`일 때 호출하면 런타임 에러가 발생한다.

- **`var` 키워드 (암시적 타입 지역 변수)**
  - 선언과 동시에 초기화되는 데이터를 보고 컴파일러가 형식을 알아서 지정해 주는 키워드이다. 지역 변수로만 사용할 수 있다.
  - **`object`와의 결정적 차이점** : `object`에 값을 넣으면 런타임에 **박싱**이 일어나 힙 메모리를 사용하므로 성능 손실이 발생하지만, `var`는 **컴파일 시점에 데이터형이 확정**되어(`var a = 3;` -> `int a = 3;`) 스택에 처리되므로 성능 저하가 전혀 없다.

```csharp
static void Main(string[] args)
{
    // 1. Nullable 형식
    int? a = null; // 값 형식인 int에 null 대입 허용
    Console.WriteLine(a.HasValue); // False (값이 없으므로)

    a = 3;
    Console.WriteLine(a.HasValue); // True
    Console.WriteLine(a.Value);    // 3 (실제 값 접근)

    // 2. var 키워드 (선언과 동시에 초기화 필수)
    var num = 3;       // 컴파일러가 알아서 int 형식으로 컴파일
    var str = "Hello"; // 컴파일러가 알아서 string 형식으로 컴파일
}
```

<br>

## 12. CTS 표준 데이터 형식 맵핑

C#의 키워드는 모두 .NET의 CTS(Common Type System) 클래스 구조에 1:1로 대응된다.

- `byte` -> `System.Byte`, <br>`int` -> `System.Int32`, <br>`long` -> `System.Int64`.
- `float` -> `System.Single`, <br>`double` -> `System.Double`, <br>`string` -> `System.String`.
  - 예를 들어, C# 코드에서 `int a = 123;`으로 선언하는 것과 `System.Int32 a = 123;`으로 선언하는 것은 완벽하게 동일하게 취급된다.
