# 2~3주차. 문자열 다루기와 특수 연산자 및 제어문

<br>

## 1. 문자열 서식화 (Formatting)와 보간

C#에서는 문자열을 원하는 형태로 조합하거나 출력하기 위해 강력한 서식화 기능을 제공한다.

- **`string.Format()` 메소드와 서식 항목**
  - 문자열의 틀을 만들고, 그 안에 데이터를 순서대로 끼워 넣어 새로운 문자열을 생성한다.
  - `{0}`, `{1}`과 같은 **'서식 항목(첨자)'**을 사용하여 매개변수의 위치를 지정한다.
  - **서식 항목의 추가 옵션 구성 (`{첨자, 맞춤: 서식 문자열}`)**
    - **맞춤 (Alignment)** : 양수를 입력하면 우측 정렬, 음수를 입력하면 좌측 정렬이 되며 지정된 숫자만큼 여백(공간)을 차지한다.
    - **변환 서식 지정 문자열** : 데이터를 특정 형태(10진수, 16진수 등)로 변환한다.
      - `D` (Decimal): 10진수 정수.
      - `X` (Hexadecimal): 16진수.
      - `N` (Number): 콤마(,)가 포함된 숫자.
      - `F` (Fixed-point): 고정 소수점.
      - `E` (Exponential): 지수 표기법.

- **문자열 보간 (String Interpolation)**
  - C# 6.0부터 새롭게 도입된 기능으로, `string.Format()`보다 **가독성이 훨씬 뛰어나고 편리한 방식**이다.
  - 문자열 따옴표(`""`) 앞에 **`$` 기호**를 붙이고, 중괄호 `{}` 안에 변수나 표현식(조건 연산자 등)을 직접 입력한다.
  - `string.Format()`에서 사용하던 '맞춤'과 '서식 지정 문자열' 옵션도 완전히 동일하게 적용할 수 있다.

```csharp
static void Main(string[] args)
{
    // 1. string.Format을 이용한 맞춤과 서식 변환
    // 첨자 0번 데이터를 10칸 공간에 우측 정렬(10)하고, N(콤마 포함 숫자) 형태로 출력
    Console.WriteLine(string.Format("Price: {0,10:N}", 123456789));
    // 첨자 0번 데이터를 10칸 공간에 좌측 정렬(-10)하고, D(10진수) 형태로 5자리 맞춰 출력
    Console.WriteLine(string.Format("Total: {0,-10:D5}", 123));

    // 2. 문자열 보간 ($)
    int age = 25;
    string name = "홍길동";
    Console.WriteLine($"이름: {name}, 나이: {age,5:D3}");

    // 3. 문자열 보간 내부에 조건 연산자 직접 사용
    int n = 123;
    Console.WriteLine($"{(n > 100 ? "큼" : "작음")}"); // 출력: 큼
}
```

<br>

## 2. 날짜 및 시간 서식화 (DateTime Formatting)

C#에서 `DateTime` 구조체를 사용하여 날짜와 시간을 원하는 형태의 문자열로 변환할 때 사용한다.

- **사용자 지정 날짜/시간 서식 지정자**
  - **연도** : `yyyy` (4자리), `yy` (2자리)
  - **월** : `MM` (2자리), `M` (1자리)
  - **일** : `dd` (2자리), `d` (1자리)
  - **요일** : `dddd` (전체 요일, 예: 토요일), `ddd` (축약 요일, 예: 토)
  - **시간** : `tt` (오전/오후), `hh` (12시간제), `HH` (24시간제), `mm` (분), `ss` (초)
- **CultureInfo 클래스** : `System.Globalization` 네임스페이스를 사용하여 문화권(예: `ko-KR`, `en-US`)에 따른 날짜 및 시간 표기 방식을 적용할 수 있다.

```csharp
using System;
using System.Globalization;

class Program
{
    static void Main(string[] args)
    {
        DateTime dt = new DateTime(2021, 1, 8, 23, 31, 17);

        // 1. 한국어권(ko-KR) 기본 표기 (문자열 보간 활용)
        Console.WriteLine($"현재 시간: {dt:yyyy-MM-dd tt hh:mm:ss (dddd)}");
        // 출력: 현재 시간: 2021-01-08 오후 11:31:17 (금요일)

        // 2. 영어권(en-US) 문화권 적용
        CultureInfo ciEn = new CultureInfo("en-US");
        Console.WriteLine(dt.ToString("yyyy-MM-dd tt hh:mm:ss (ddd)", ciEn));
        // 출력: 2021-01-08 PM 11:31:17 (Fri)
    }
}
```

<br>

## 3. 문자열 조작 메소드와 결합 연산자

C#의 `string` 객체는 문자열을 쉽게 다루기 위한 다양한 내장 메소드와 결합 연산자를 제공한다.

- **문자열 결합 연산자 (`+`)** : 두 문자열 사이에 `+`를 사용하여 하나의 문자열로 연결한다.
- **문자열 찾기 (탐색)**
  - `IndexOf()`, `LastIndexOf()` : 특정 문자열을 앞/뒤에서 찾아 인덱스를 반환한다 (없으면 -1).
  - `StartsWith()`, `EndsWith()` : 특정 문자열로 시작하거나 끝나는지 검사하여 `bool`을 반환한다.
  - `Contains()` : 특정 문자열이 포함되어 있는지 `bool`을 반환한다.
  - `Replace()` : 특정 문자열을 찾아 다른 문자열로 모두 바꾼다.
- **문자열 변형하기**
  - `ToLower()`, `ToUpper()` : 전체를 소문자/대문자로 변환한다.
  - `Insert()`, `Remove()` : 지정된 인덱스 위치에 문자열을 삽입하거나, 수만큼 삭제한다.
  - `Trim()`, `TrimStart()`, `TrimEnd()` : 문자열 앞/뒤의 공백을 제거한다.
- **문자열 분할하기**
  - `Split()` : 지정된 문자를 기준으로 문자열을 쪼개어 `string[]` (배열)로 반환한다.
  - `Substring()` : 지정된 위치부터 특정 길이만큼 문자열을 잘라내어 반환한다.

```csharp
static void Main(string[] args)
{
    // 1. 문자열 결합 연산자
    string combine = "123" + "456"; // "123456"

    // 2. 문자열 탐색 및 변형
    string greeting = "Good Morning";
    Console.WriteLine(greeting.IndexOf("o")); // 1 (첫 번째 'o'의 위치)
    Console.WriteLine(greeting.StartsWith("Good")); // True
    Console.WriteLine(greeting.Replace("Morning", "Evening")); // Good Evening

    string text = "  No Spaces  ";
    Console.WriteLine($"'{text.Trim()}'"); // 'No Spaces'
    Console.WriteLine("Happy Friday!".Insert(6, "Sunny ")); // Happy Sunny Friday!

    // 3. 문자열 분할 및 추출
    string[] arr = greeting.Split(" ", StringSplitOptions.None); // 공백 기준으로 "Good", "Morning" 배열화
    Console.WriteLine(greeting.Substring(0, 4)); // 0번부터 4글자 잘라냄 -> Good
}
```

<br>

## 4. Null 관련 특수 연산자 (중요)

모던 C#에서 객체가 비어있는지(null) 검사하고 안전하게 에러를 방지하기 위해 도입된 핵심 연산자들이다.

- **`?.` (null 조건부 연산자)**
  - C# 6.0부터 지원된다.
  - 객체의 멤버(프로퍼티나 메소드)에 접근하기 전, **해당 객체가 null인지 검사**한다.
  - 객체가 `null`이면 멤버에 접근하지 않고 그 결과로 `null`을 반환(에러 방지)하며, `null`이 아니면 정상적으로 뒤에 지정된 멤버 값을 반환한다.
- **`??` (null 병합 연산자)**
  - 두 피연산자에 대해 왼쪽 피연산자가 `null`인지 평가한다.
  - 평가 결과가 `null`이 아니면 왼쪽 피연산자를 그대로 반환하고, `null`이면 오른쪽 피연산자를 반환(대체값 제공)한다.

```csharp
class Foo { public int member = 10; }

static void Main(string[] args)
{
    // 1. null 조건부 연산자 (?.)
    Foo foo = null;

    // foo가 null이므로 런타임 에러(NullReferenceException)를 발생시키지 않고 bar에 null을 대입함
    int? bar = foo?.member;

    // 2. null 병합 연산자 (??)
    int? num = null;

    // num이 null이므로 대체값인 0을 반환함
    Console.WriteLine($"{num ?? 0}"); // 출력: 0

    num = 99;
    // num이 null이 아니므로 왼쪽의 99를 그대로 반환함
    Console.WriteLine($"{num ?? 0}"); // 출력: 99
}
```

<br>

## 5. 비트 연산자 및 할당 연산자

데이터를 비트 단위로 조작하거나 변수에 값을 재할당할 때 사용한다.

- **비트 시프트 연산자 (`<<`, `>>`)** : 지정한 수만큼 비트를 이동시킨다. $a \ll b$는 $a \times 2^b$, $a \gg b$는 $a \div 2^b$와 같은 고속 연산을 수행한다.
  - **음수의 시프트 연산** : 음수를 오른쪽 시프트(`>>`)하면 빈자리는 부호를 유지하기 위해 0이 아닌 1로 채워진다.
- **비트 논리 연산자 (`&`, `|`, `^`, `~`)**
  - `&` (논리곱 AND), `|` (논리합 OR).
  - `^` (배타적 논리합 XOR) : 두 비트의 진리값이 서로 달라야 1을 반환한다.
  - `~` (보수 NOT) : 피연산자의 비트를 0은 1로, 1은 0으로 반전시킨다.
- **할당 연산자 (`+=`, `-=`, `*=`, `%=`, `<<=`, `^=` 등)** : 왼쪽 피연산자와 오른쪽 피연산자를 연산한 결과를 다시 왼쪽 피연산자에 할당한다 (`a += b;`는 `a = a + b;`와 동일).

```csharp
static void Main(string[] args)
{
    // 1. 비트 연산자
    int a = 9;  // 1001
    int b = 10; // 1010

    Console.WriteLine($"a & b: {a & b}"); // 1000 -> 8
    Console.WriteLine($"a | b: {a | b}"); // 1011 -> 11
    Console.WriteLine($"a ^ b: {a ^ b}"); // 0011 -> 3
    Console.WriteLine($"~a: {~a}");       // 비트 반전 -> -10

    int c = -255;
    Console.WriteLine($"c >> 2: {c >> 2}"); // 음수 시프트: 빈자리가 1로 채워져 -64

    // 2. 복합 할당 연산자
    int num = 100;
    num += 90; // 190
    num <<= 1; // 190을 왼쪽 1비트 시프트 (곱하기 2) -> 380
}
```

<br>

## 6. 제어문 (조건문과 반복문)의 심화 (C# 고유 기능 포함)

C#의 분기문(`switch`)과 반복문(`foreach`)은 타 언어 대비 확장된 강력한 기능을 제공한다.

- **`TryParse()` 메소드와 `out` 키워드**
  - 문자열을 숫자형식 데이터로 변환하며, 성공 시 `true`, 실패 시 `false`를 반환한다.
  - 변환된 결과값은 `out` 키워드를 통해 외부 매개변수로 안전하게 넘겨준다.
- **데이터 형식 분기와 `when` 절 (`switch` 문)**
  - C# 7.0부터 `switch` 문의 조건으로 문자열뿐만 아니라 객체의 데이터 형식 자체를 사용할 수 있다 (`case "ABC":`, `case int i:`).
  - `when` 절을 추가하여, 조건식을 통과한 후에도 한 번 더 구체적인 조건을 검사할 수 있다.
- **`switch` 식 (Switch Expression)**
  - C# 8.0부터 지원되며, 분기를 거쳐 값을 변수에 바로 할당(반환)해야 할 때 코드를 획기적으로 줄여준다.
  - 조건식을 `switch` 키워드 앞으로 옮기고, `case` 대신 `=>`를, `break` 대신 콤마(`,`)를, `default` 대신 언더바(`_`)를 사용한다.
- **`foreach` 문**
  - 배열이나 컬렉션을 순회하며 각 데이터 요소에 차례로 접근한다.
  - `foreach (데이터형식 변수명 in 배열)` 형태로 사용하며, 끝에 도달하면 반복이 자동 종료된다.

```csharp
static void Main(string[] args)
{
    // 1. TryParse와 out 키워드를 이용한 형 변환 검사
    string s = "123.45";
    object obj = null;

    if (int.TryParse(s, out int out_i)) obj = out_i;
    else if (float.TryParse(s, out float out_f)) obj = out_f; // 이 부분이 실행됨
    else obj = s;

    // 2. switch 문 (데이터 형식 분기 및 when 절)
    switch (obj)
    {
        case int i:
            Console.WriteLine($"{i}는 int 형식");
            break;
        case float f when f >= 0: // 데이터 형식이 float이면서 양수일 때 통과
            Console.WriteLine($"{f}는 양의 float 형식"); // 출력됨
            break;
        default:
            break;
    }

    // 3. switch 식 (Switch Expression)
    int score = 85;
    bool repeated = true;

    string grade = (score / 10 * 10) switch
    {
        90 => "A",
        80 when repeated == true => "B+", // switch 식에서도 when 절 사용 가능
        80 => "B",
        _ => "F"  // _ 는 default를 의미
    };
    Console.WriteLine($"학점: {grade}"); // 출력: 학점: B+

    // 4. foreach 문
    int[] arr = new int[] { 10, 20, 30 };
    foreach (int a in arr) // 배열의 각 요소를 a에 하나씩 대입하며 자동 순회
    {
        Console.WriteLine(a);
    }
}
```

<br>

## 7. 점프문 (`break`, `continue`, `goto`)

프로그램의 반복이나 특정 실행 흐름을 강제로 이동시키는 제어문이다.

- **`break` 문** : 현재 실행 중인 반복문(`while`, `for`)이나 `switch` 문의 실행을 완전히 중단하고 바깥으로 빠져나간다.
- **`continue` 문** : 반복문에서 현재 회차의 남은 코드를 건너뛰고 바로 다음 반복(조건 검사 또는 증감식)을 계속 수행한다.
- **`goto` 문** : 코드 안에 지정된 `레이블:` 위치로 단숨에 점프한다. 중첩된 반복문에서 한 번에 빠져나올 때 유용할 수 있으나, 가독성을 크게 떨어뜨리는 단점이 있어 남용을 주의해야 한다.

```csharp
static void Main(string[] args)
{
    // 1. break와 continue
    for (int i = 0; i < 10; i++)
    {
        if (i % 2 == 0) continue; // 짝수면 아래 출력문을 건너뛰고 다음 반복 진행
        if (i > 5) break;         // 5보다 커지면 즉시 반복문 완전히 탈출

        Console.WriteLine($"{i} : 홀수"); // 1, 3, 5 출력
    }

    // 2. goto 문
    Console.WriteLine("1");
    goto JUMP; // JUMP 레이블 위치로 즉시 점프하므로 아래 2, 3은 실행되지 않음

    Console.WriteLine("2");
    Console.WriteLine("3");

JUMP: // 레이블 위치 선언
    Console.WriteLine("4"); // 출력: 1, 4
}
```
