# 3~4주차. 객체지향과 클래스, 그리고 메소드 심화

<br>

## 1. 객체지향 프로그래밍과 클래스

현실 세계의 모든 사물(객체, Object)을 속성(데이터)과 기능(메소드)으로 모델링하여 프로그래밍하는 패러다임이다.

- **클래스 (Class)** : 객체를 만들기 위한 '청사진' 또는 '틀'이다. 클래스 내부에 선언된 변수를 필드(Field)라 하고, 필드, 메소드, 프로퍼티 등 클래스 내에 선언되는 모든 요소를 멤버(Member)라고 한다.
- **객체 (Object)와 인스턴스 (Instance)** : 클래스라는 틀을 통해 실제 메모리상에 구현된 실체를 의미한다.

### `new` 키워드와 객체 생성

- 클래스는 참조 형식(Reference Type)이다.
- `new` 연산자는 힙(Heap) 메모리 영역에 객체를 위한 공간을 할당하고, 그 공간을 가리키는 메모리 주소(참조값)를 스택(Stack) 영역의 변수에게 반환한다.

```csharp
// 클래스 선언
class Cat
{
    public string Name; // 필드 선언
    public string Color;

    public void Meow() // 메소드 선언
    {
        Console.WriteLine($"{Name} : 야옹");
    }
}

class Program
{
    static void Main(string[] args)
    {
        // new 키워드를 통해 힙 영역에 객체 생성 후 참조값을 kitty 변수에 할당
        Cat kitty = new Cat();
        kitty.Color = "하얀색";
        kitty.Name = "키티";
        kitty.Meow(); // 출력: 키티 : 야옹

        Cat nero = new Cat();
        nero.Color = "검은색";
        nero.Name = "네로";
        nero.Meow(); // 출력: 네로 : 야옹
    }
}
```

<br>

## 2. 생성자 (Constructor)와 종료자 (Destructor)

- **생성자**
  - 클래스와 이름이 동일하며 반환 형식이 없는 특별한 메소드이다.
  - 객체가 생성(`new`)될 때 자동으로 호출되어 객체의 초기화를 담당한다.
  - 매개변수를 다르게 하여 여러 개의 생성자를 오버로딩(Overloading)할 수 있다.
  - 프로그래머가 생성자를 하나도 정의하지 않으면, 컴파일러가 매개변수 없는 '기본 생성자(Default Constructor)'를 자동으로 제공한다.

- **종료자 (`~클래스명()`)**
  - 객체가 메모리에서 소멸될 때 호출되는 메소드이다.
  - 반환형과 매개변수가 없으며, 한정자(`public` 등)도 사용할 수 없고 오버로딩도 불가능하다.
  - **주의점**: C#은 가비지 컬렉터(GC)가 메모리 해제를 전담하므로, 종료자가 언제 호출될지 정확히 예측할 수 없다. 또한 종료자를 사용하면 GC의 성능을 저하시킬 수 있으므로 **명시적인 종료자 사용은 지양**해야 한다.

```csharp
class Cat
{
    public string Name;
    public string Color;

    // 기본 생성자
    public Cat()
    {
        Name = "";
        Color = "";
    }

    // 오버로딩된 생성자
    public Cat(string _Name, string _Color)
    {
        Name = _Name;
        Color = _Color;
    }

    // 종료자 (가비지 컬렉터에 의해 소멸될 때 실행됨)
    ~Cat()
    {
        Console.WriteLine($"{Name} : 잘가");
    }
}

class Program
{
    static void Main(string[] args)
    {
        Cat kitty = new Cat("키티", "하얀색"); // 오버로딩된 생성자 호출
        Cat nero = new Cat("네로", "검은색");
    }
    // Main 블록 종료 후 언젠가 가비지 컬렉터가 수집할 때 "키티 : 잘가", "네로 : 잘가"가 출력됨
}
```

<br>

## 3. 메소드 (Method) 기본과 실행 흐름

C언어의 함수와 같은 개념으로, C#에서는 반드시 클래스 내부에 존재해야 하는 기능적 단위이다.

- **매개변수와 반환 형식**
  - **매개변수** : 메소드 안에서 연산에 사용하기 위해 외부로부터 입력받는 변수이다.
  - **반환 형식** : 메소드 수행 후 호출자에게 돌려줄 결과값의 데이터형이다. 반환할 수행 결과가 없는 메소드라면 `void`를 사용한다.
- **선언 형식** : `한정자 반환_형식 메소드_이름(매개변수_목록) { /* 실행 코드 */ return 반환값; }`.
- **실행 흐름** : 호출자가 인수를 넘겨 메소드를 호출하면 프로그램의 흐름이 메소드 내부로 점프하며, 블록 끝에 도달하거나 `return` 문을 만나면 메소드가 종결되고 다시 호출했던 곳으로 흐름이 되돌아온다.

```csharp
// 메소드를 포함한 클래스
class Calculator
{
    public int Plus(int a, int b) // 반환형식이 int인 메소드
    {
        return a + b; // 덧셈 결과를 호출자에게 반환
    }

    public int Minus(int a, int b)
    {
        return a - b;
    }
}

class MainApp
{
    static void Main(string[] args)
    {
        Calculator Calc = new Calculator();

        // 1. Plus 메소드 호출 (흐름 이동) -> 2. 연산 수행 -> 3. 결과 반환 후 흐름 복귀
        int x = Calc.Plus(3, 4);
        Console.WriteLine($"3+4={x}"); // 출력: 3+4=7

        int y = Calc.Minus(5, 2);
        Console.WriteLine($"5-2={y}"); // 출력: 5-2=3
    }
}
```

<br>

## 4. 정적 (`static`) 필드와 메소드

`static` 키워드는 메소드나 필드가 개별 '객체'가 아닌 '**클래스 자체**'에 소속되도록 지정한다.

- 객체를 생성(`new`)하지 않고도 `클래스명.필드명` 형태로 즉시 접근할 수 있다.
- 프로그램 전체에서 유일한 메모리 공간을 가지므로, 모든 객체가 값을 공유하는 **전역 변수/함수**처럼 사용된다.

```csharp
class Global
{
    public static int Count = 0; // 정적 필드
    public static void Print() { Console.WriteLine(Count); } // 정적 메소드
}

class Program
{
    static void Main(string[] args)
    {
        // 객체 생성 없이 클래스 이름으로 직접 접근 및 공유
        Global.Count++;
        Global.Print(); // 출력: 1
    }
}
```

<br>

## 5. 매개변수 전달 방식 (`ref`와 `out`)

기본적으로 C#의 메소드는 매개변수를 '값에 의한 호출(Call by value)'로 전달하여 원본 데이터를 훼손하지 않는다. 하지만 원본 변수 자체를 조작해야 할 때는 특별한 키워드를 사용한다.

- **`ref` 키워드 (참조에 의한 호출)**
  - 변수의 실제 메모리 주소를 메소드로 전달하여, 메소드 내부의 수정 사항이 원본 변수에도 그대로 반영된다.
  - **주의점**: 메소드로 전달하기 전에 변수가 **반드시 초기화**되어 있어야 한다.

```csharp
static void Swap(ref int a, ref int b)
{
    int temp = b;
    b = a;
    a = temp;
}

static void Main(string[] args)
{
    int x = 3;
    int y = 4;

    // 변수 앞에도 ref 키워드를 붙여서 참조로 전달함을 명시
    Swap(ref x, ref y);
    Console.WriteLine($"x:{x}, y:{y}"); // 출력: x:4, y:3 (원본 값이 변경됨)
}
```

- **`out` 키워드 (출력 전용 매개변수)**
  - 하나의 메소드에서 여러 개의 결과값을 반환받고 싶을 때 사용한다.
  - `ref`와 다르게 **전달하기 전 변수의 초기화가 필요 없다**.
  - **주의점**: 해당 메소드 내부에서 `out` 매개변수에 값을 **반드시 할당**해야만 컴파일 에러가 발생하지 않는다.

```csharp
// 몫과 나머지를 동시에 반환하는 메소드
static void Divide(int a, int b, out int quotient, out int remainder)
{
    quotient = a / b;
    remainder = a % b; // out 매개변수에 값을 반드시 할당해야 함
}

static void Main(string[] args)
{
    int a = 20;
    int b = 3;

    // 호출 시 타입과 함께 out 변수를 즉시 선언하여 받을 수 있음 (C# 7.0+)
    Divide(a, b, out int c, out int d);

    Console.WriteLine($"a:{a}, b:{b}, a/b:{c}, a%b:{d}");
    // 출력: a:20, b:3, a/b:6, a%b:2
}
```

- **메소드 결과를 참조로 반환 (`ref return`)**
  - 반환형 앞과 `return` 뒤에 모두 `ref`를 붙여, 값의 복사가 아닌 변수의 참조 자체를 반환할 수 있다.
  * C/C++에서 포인터나 참조자를 반환하여 외부에서 내부 데이터를 직접 조작할 수 있게 하는 것과 완전히 동일한 원리이다.

- `ref` 키워드를 총 4곳에 명시해야 완벽하게 참조로 연결된다.
  1. 메소드 선언부의 반환 형식 앞 (`public ref int ...`)
  2. 메소드 내부의 `return` 키워드 뒤 (`return ref price;`)
  3. 호출하는 쪽의 지역 변수 타입 앞 (`ref int local_var = ...`)
  4. 호출하는 메소드 이름 앞 (`... = ref obj.Method();`)

```csharp
class Product
{
    private int price = 100;

    // ref int 반환형
    public ref int GetPrice()
    {
        return ref price; // 변수의 참조 반환
    }
    public void PrintPrice() { Console.WriteLine($"Price: {price}"); }
}

static void Main(string[] args)
{
    Product carrot = new Product();

    // 반환받은 참조를 ref 지역 변수에 연결
    ref int ref_local_price = ref carrot.GetPrice();
    ref_local_price = 200; // 참조를 통해 값을 변경하면 원본(Product.price)도 변경됨

    carrot.PrintPrice(); // 출력: Price: 200
}
```

<br>

## 6. 다양한 매개변수와 오버로딩 (Overloading)

- **메소드 오버로딩** : 이름이 같더라도 매개변수의 개수나 데이터 형식이 다르면 여러 개의 메소드를 중복해서 선언할 수 있다. 컴파일러가 호출 시점의 인자를 보고 적절한 메소드를 찾아 실행한다.
- **가변 길이 매개변수 (`params`)** : 인자의 개수가 유동적일 때 배열 형태로 받는다.
- **명명된 매개변수** : 인자를 전달할 때 `변수명: 값` 형태로 매개변수 이름을 명시하여 순서에 상관없이 값을 전달할 수 있다.
- **선택적 매개변수** : 매개변수에 기본값을 미리 할당해두면, 호출자가 해당 인자를 생략할 경우 기본값이 사용된다. (단, 항상 매개변수 목록의 가장 마지막에 선언되어야 한다).
  - 주의 : 선택적 매개변수를 쓸 때, 오버로딩이랑 같이 쓰면 혼란이 야기된다.
    <br>메소드를 쓸 때 인자 하나만 썼다고 해보자.
    <br>선택적 인수로 보면 두번째 인자는 기본값으로 해서 실행하려 할테고,
    <br>오버로딩으로 보면 인자 하나짜리 생성자를 실행하려 할테니 뭘 원하는지 혼란스럽다.

```csharp
// 1. 가변 길이 매개변수 (params)
static int Sum(params int[] args)
{
    int sum = 0;
    foreach (int a in args) sum += a;
    return sum;
}

// 2. 선택적 매개변수 (기본값 할당)
static void PrintProfile(string name, string phone = "010-000-0000")
{
    Console.WriteLine($"Name:{name}, Phone:{phone}");
}

static void Main(string[] args)
{
    // 가변 길이 매개변수 사용
    int total = Sum(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
    Console.WriteLine($"Sum: {total}"); // 출력: Sum: 55

    // 3. 명명된 매개변수 (순서 상관 없음)
    PrintProfile(phone: "010-123-1234", name: "동이"); // 출력: Name:동이, Phone:010-123-1234

    // 선택적 매개변수 활용 (phone 생략)
    PrintProfile("태희"); // 출력: Name:태희, Phone:010-000-0000
}
```

<br>

## 7. 로컬 함수 (Local Functions)

- 클래스 멤버가 아닌, **메소드 내부에서 또 다른 메소드를 선언**하여 사용하는 기능이다 (C# 7.0+ 도입).
- 부모 메소드 내에서만 호출 가능하며, 부모 메소드의 지역 변수들을 그대로 읽고 쓸 수 있어 가독성을 높인다.

```csharp
static string ToLowerString(string input)
{
    var arr = input.ToCharArray();
    for (int i = 0; i < arr.Length; i++)
    {
        arr[i] = ToLowerChar(i); // 로컬 함수 호출
    }

    // 부모 메소드의 변수(arr)에 자유롭게 접근하는 로컬 함수
    char ToLowerChar(int i)
    {
        if (arr[i] < 65 || arr[i] > 90) return arr[i];
        else return (char)(arr[i] + 32);
    }
    return new string(arr);
}

static void Main(string[] args)
{
    Console.WriteLine(ToLowerString("Hello!")); // 출력: hello!
}
```

<br>

## 8. 객체 복사 (얕은 복사 vs 깊은 복사)

클래스는 참조 형식이므로 단순 대입 연산자(`=`)를 사용하면, 데이터가 복사되는 것이 아니라 힙 메모리를 가리키는 스택의 **'참조 주소'**만 복사된다.

- **얕은 복사 (Shallow Copy)** : 객체를 대입(`=`)하여 주소만 복사하는 현상. 두 변수가 동일한 힙 메모리 객체를 가리키게 되므로, 한쪽을 수정하면 다른 쪽도 훼손된다.
- **깊은 복사 (Deep Copy)** : 힙 메모리에 완전히 새로운 객체(별도의 공간)를 할당하고, 원본 객체의 필드값들을 그대로 옮겨 담는 과정이다. 개발자가 `new`를 통해 명시적으로 구현해주어야 한다.

```csharp
class MyClass
{
    public int MyField1;
    public int MyField2;

    // 깊은 복사를 위한 커스텀 메소드 구현
    public MyClass DeepCopy()
    {
        MyClass newCopy = new MyClass(); // 새로운 메모리 할당
        newCopy.MyField1 = this.MyField1; // 값 복사
        newCopy.MyField2 = this.MyField2;
        return newCopy;
    }
}

static void Main(string[] args)
{
    MyClass source = new MyClass();
    source.MyField1 = 10;
    source.MyField2 = 20;

    // 1. 얕은 복사
    MyClass target_shallow = source;
    target_shallow.MyField2 = 30; // 얕은 복사본을 수정하면
    Console.WriteLine($"{source.MyField1} {source.MyField2}"); // 원본도 바뀜 (출력: 10 30)

    // 2. 깊은 복사
    MyClass target_deep = source.DeepCopy();
    target_deep.MyField2 = 40; // 깊은 복사본을 수정해도
    Console.WriteLine($"{source.MyField1} {source.MyField2}"); // 원본은 유지됨 (출력: 10 30)
}
```

<br>

## 9. `this` 키워드

- **`this` 키워드** : 객체 내부에서 자기 자신의 멤버(필드/메소드)에 접근할 때 사용한다 (예: 매개변수와 필드 이름이 같을 때 구분하기 위함).
- **`this()` 생성자** : 오버로딩된 생성자들 사이에서 **자기 자신의 또 다른 생성자를 호출**할 때 사용하며, 중복된 코드를 획기적으로 줄여준다.

```csharp
class MyClass
{
    int a, b, c;

    // 1. 기본 생성자
    public MyClass()
    {
        this.a = 5425; // this 키워드로 자신의 필드에 접근
    }

    // 2. this() 생성자 활용
    // MyClass(int b)가 호출되면, 먼저 MyClass()를 호출하여 a=5425를 세팅한 후 이어서 실행됨
    public MyClass(int b) : this()
    {
        this.b = b;
    }

    // MyClass(int b)를 먼저 호출해서 a=5425, b를 먼저 세팅하고 c를 입력받는다
    public MyClass(int b, int c) : this(b)
    {
        this.c = c;
    }
}
```

<br>

## 10. 접근 제한자 (Access Modifiers)

클래스 외부에서 내부 멤버로의 접근을 통제하기 위해 사용한다.

- 은닉성 (캡슐화)
  - 클래스의 사용자에게 필요한 최소의 기능만 노출하고 내부를 감춘다.

- **종류**
  - `public` : 클래스의 내/외부 모든 곳에서 자유롭게 접근 가능.
  - `protected` : 클래스 내부 및 해당 클래스를 **상속받은 파생 클래스**에서만 접근 가능.
  - `private` : **클래스 내부에서만 접근 가능** (가장 엄격한 제한).
  - `internal` : 같은 어셈블리(프로젝트) 내에서만 `public`처럼 접근 가능.
  - `protected internal`, `private protected` : 어셈블리와 상속 관계를 조합한 제한 방식.
- **기본 접근 수준** : 클래스 멤버 선언 시 접근 제한자를 명시하지 않고 생략하면, 컴파일러는 이를 무조건 **`private`**으로 간주한다.

```csharp
class WaterHeater
{
    // 접근 제한자 생략 또는 private 시 외부(Main 등)에서 직접 접근 불가
    protected int temperature;

    public void SetTemperature(int temperature) // public이므로 외부 호출 가능
    {
        if (temperature < -5 || temperature > 42)
        {
            throw new Exception("Out of temperature range");
        }
        this.temperature = temperature; // 클래스 내부이므로 protected 멤버에 안전하게 접근
    }

    internal void TurnOnWater()
    {
        Console.WriteLine($"Turn on water: {temperature}");
    }
}
```
