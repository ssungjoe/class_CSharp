# 4~6주차. 객체지향의 3대 핵심: 캡슐화, 상속성, 다형성

<br>

## 1. 정보 은닉(Information Hiding)과 프로퍼티 (Property)

클래스의 필드(데이터)를 외부에서 무분별하게 조작하지 못하도록 방어하고, 안전한 접근만을 허용하는 객체지향의 첫 번째 원칙이다.

- **접근자(Getter)와 설정자(Setter) 메소드의 한계**
  - 기존 C++이나 Java에서는 필드를 `private`으로 숨기고, `Get()`, `Set()` 메소드를 일일이 만들어 데이터에 대한 검증 로직(예: 나이가 0보다 작을 수 없음)을 구현했다.
  - 하지만 이는 코드를 지나치게 길어지게 만들고 가독성을 떨어뜨리는 단점이 있다.

- **C# 프로퍼티 (Property)**
  - C#은 접근자/설정자를 메소드 없이 변수처럼 직관적으로 사용할 수 있는 '프로퍼티' 문법을 제공한다.
  - 내부적으로는 `get`과 `set` 접근자를 가지며, `set` 내부에서는 외부에서 대입한 값을 **`value`**라는 암묵적 예약어(매개변수)로 받아 처리한다.
- **자동 구현 프로퍼티 (Auto-Implemented Property)**
  - 특별한 검증 로직이 필요 없다면, 필드 선언조차 생략하고 `public int Age { get; set; }` 형태로 극도로 축약할 수 있다 (C# 3.0 이상).
- **초기화 전용 (`init`) 접근자**
  - `set` 대신 `init`을 사용하면, 객체를 **처음 생성(초기화)할 때 딱 한 번만 값을 할당**할 수 있고 이후에는 읽기 전용으로 완전히 굳어져 버리는 불변(Immutable) 프로퍼티를 만들 수 있다 (C# 9.0 이상).

```csharp
class Person
{
    // 1. 일반적인 프로퍼티 (내부 필드 필요, 로직 포함 가능)
    private int age;
    public int Age
    {
        get { return age; }
        set
        {
            if (value < 0) value = 0; // 암묵적 매개변수 value를 사용한 데이터 검증
            age = value;
        }
    }

    // 2. 자동 구현 프로퍼티 (가장 많이 쓰임)
    public string Name { get; set; } = "Unknown"; // C# 7.0부터 선언과 동시에 초기화 가능

    // 3. init 접근자 (초기화 전용)
    public string SocialID { get; init; }
}

static void Main(string[] args)
{
    // 객체 생성과 동시에 프로퍼티 초기화 (객체 초기자 문법)
    Person p = new Person() { Name = "홍길동", Age = -5, SocialID = "900101-1234567" };

    Console.WriteLine(p.Age); // 출력: 0 (음수 방어 로직 작동)
    // p.SocialID = "새로운값"; // 컴파일 에러 발생! (init 프로퍼티는 생성 이후 변경 불가)
}
```

<br>

## 2. 레코드 (`record`)와 무명 형식

- **레코드 (`record`) 객체 (C# 9.0+)**
  - 불변(Immutable) 객체를 쉽게 만들기 위해 도입된 참조 형식이다.
  - **가장 큰 특징 (값 기반 동등성)** : 일반 `class`는 두 객체의 내부 값이 같더라도 힙(Heap) 메모리 주소가 다르면 `Equals()` 비교 시 `false`를 반환한다. 하지만 `record`는 **내부 상태(값)가 동일하면 완전히 같은 객체로 취급하여 `true`를 반환**한다.
- **무명 형식 (Anonymous Types)**
  - 임시로 한 번 쓰고 버릴 데이터 묶음이 필요할 때, 클래스를 선언하지 않고 `new { Name = "홍길동", Age = 123 }` 형태로 즉석에서 객체를 만든다.
  - 할당된 프로퍼티는 **무조건 읽기 전용**이 되어 값을 바꿀 수 없다.

```csharp
class CTransaction { public string From { get; init; } }
record RTransaction { public string From { get; init; } }

static void Main()
{
    // 1. 일반 클래스의 비교 (주소 비교)
    CTransaction c1 = new CTransaction { From = "Alice" };
    CTransaction c2 = new CTransaction { From = "Alice" };
    Console.WriteLine(c1.Equals(c2)); // 출력: False (주소가 다름)

    // 2. 레코드의 비교 (값 기반 동등성 비교)
    RTransaction r1 = new RTransaction { From = "Alice" };
    RTransaction r2 = new RTransaction { From = "Alice" };
    Console.WriteLine(r1.Equals(r2)); // 출력: True (내부 값이 같으므로)

    // 3. 무명 형식 사용
    var tempObj = new { Title = "C# Study", Score = 100 };
    // tempObj.Score = 90; // 무명 형식은 읽기 전용이므로 컴파일 에러
}
```

- `with` 키워드를 이용한 레코드 복사
  <br>레코드(`record`) 객체는 주로 `init` 접근자를 사용하여 한 번 값이 정해지면 바꿀 수 없는 불변(Immutable) 상태로 사용된다. 이때 기존 객체의 데이터는 대부분 그대로 유지하면서 **특정 필드의 값만 살짝 바꾼 새로운 객체를 만들고 싶을 때** `with` 키워드를 사용한다.
  - **깊은 복사 (Deep Copy) 수행** : `with` 키워드를 사용하면 두 `record` 객체 사이의 깊은 복사가 자동으로 깔끔하게 수행된다.
  - **일부 필드 값 변경** : 복사와 동시에 중괄호 `{ }` 안에 새롭게 변경하고 싶은 프로퍼티의 값만 지정해주면, 나머지는 원본 객체의 값을 그대로 가져온다.

```csharp
record RTransaction
{
    // 초기화 이후 변경 불가능한(init) 프로퍼티들
    public string From { get; init; }
    public string To { get; init; }
    public int Amount { get; init; }
}

static void Main(string[] args)
{
    // 원본 레코드 객체 생성
    RTransaction tr1 = new RTransaction { From = "Alice", To = "Bob", Amount = 100 };

    // 1. tr1을 깊은 복사하되, 'To' 프로퍼티 값만 "Charlie"로 변경하여 새로운 객체 생성
    RTransaction tr2 = tr1 with { To = "Charlie" };

    // 2. 콤마(,)를 사용하여 여러 개의 필드 값도 동시에 변경 가능
    RTransaction tr3 = tr1 with { From = "Charlie", Amount = 50 };

    Console.WriteLine($"{tr1.From} -> {tr1.To} : {tr1.Amount}");
    // 출력: Alice -> Bob : 100

    Console.WriteLine($"{tr2.From} -> {tr2.To} : {tr2.Amount}");
    // 출력: Alice -> Charlie : 100 (To만 변경됨)

    Console.WriteLine($"{tr3.From} -> {tr3.To} : {tr3.Amount}");
    // 출력: Charlie -> Bob : 50 (From과 Amount가 변경됨)
}
```

<br>

## 3. 상속성 (Inheritance)의 깊은 이해

부모(기반) 클래스의 모든 멤버를 자식(파생) 클래스가 그대로 물려받아 코드를 재사용하고 확장하는 개념이다.

- **상속 문법** : `class 파생클래스명 : 기반클래스명`
- **`object` 클래스** : C#의 모든 클래스는 명시하지 않아도 암묵적으로 최상위 부모인 `System.Object`를 상속받는다.

- **`protected` 접근 제한자 (상속의 핵심)**
  - 클래스 외부에서는 멤버에 대한 접근을 완벽하게 차단(`private`과 동일하게 동작)하지만, **자신을 상속받은 자식(파생) 클래스 내부에서만큼은 예외적으로 접근을 허용**하는 접근 제한자이다. 부모의 유산을 자식에게만 몰래 물려줄 때 사용한다.

- **생성자와 종료자의 호출 순서 (매우 중요)**
  - 자식 객체를 생성하면, **부모의 생성자가 먼저 실행**되어 기반을 다진 후 자식의 생성자가 실행된다.
  - 객체가 소멸할 때는 역순으로 **자식의 종료자가 먼저 실행**되고, 마지막에 부모의 종료자가 실행된다.

- **`base` 와 `base()` 키워드**
  - `base` : 부모 클래스의 멤버에 명시적으로 접근할 때 사용한다 (예: `base.Method()`).
  - `base()` : **부모 클래스의 생성자를 명시적으로 호출**한다. 매개변수가 있는 부모 생성자를 반드시 호출해야 할 때 자식 생성자 선언부 뒤에 `: base(매개변수)` 형태로 붙여서 사용한다.
- **`sealed` (봉인 클래스)** : 클래스 선언 시 `sealed` 한정자를 붙이면, 더 이상 다른 클래스가 이 클래스를 상속받지 못하도록 원천 차단한다.

```csharp
class Base
{
    protected string Name;
    public Base(string name) // 부모의 생성자
    {
        this.Name = name;
        Console.WriteLine($"{this.Name}.Base() 생성자 호출");
    }
    ~Base() { Console.WriteLine($"{this.Name}.~Base() 소멸"); }
}

// 상속 및 부모 생성자 명시적 호출 (base)
class Derived : Base
{
    // 매개변수를 받아 부모 생성자(base)로 그대로 넘겨줌
    public Derived(string name) : base(name)
    {
        Console.WriteLine($"{this.Name}.Derived() 생성자 호출");
    }
    ~Derived() { Console.WriteLine($"{this.Name}.~Derived() 소멸"); }
}

/* 객체 생성 시 출력 순서:
   1. b.Base() 생성자 호출
   2. b.Derived() 생성자 호출
   ... 프로그램 종료 시 ...
   3. b.~Derived() 소멸
   4. b.~Base() 소멸
*/
```

<br>

## 부모/자식 클래스 사이의 형식 변환과 활용 (업캐스팅/다운캐스팅)

상속 관계에 있는 클래스 간에는 데이터 형식(타입)을 변환할 수 있으며, 이를 통해 객체지향의 장점인 코드의 재사용성과 생산성을 극대화할 수 있다.

- **암시적 형변환 (Upcasting)**
  - 자식 클래스의 객체를 부모 클래스 형식의 변수에 대입하는 것이다.
  - 범위가 좁은 곳(특수)에서 넓은 곳(일반)으로 가는 것이므로 컴파일러가 아무런 부가 코드 없이 자동으로 안전하게 처리해 준다.
- **명시적 형변환 (Downcasting)**
  - 부모 클래스 형식으로 업캐스팅되어 있던 변수를 다시 본래의 자식 타입으로 강제 복귀시키는 것이다.
  - 캐스팅 연산자 `()`를 사용해야 하며, 만약 변수 안의 실제 알맹이가 해당 자식 타입이 아닐 경우 컴파일은 되더라도 런타임 에러가 발생할 수 있다.

형변환의 핵심 활용 목적 (왜 사용하는가?)
<br>만약 부모/자식 간의 암시적 형변환이 없다면, 자식 클래스가 10개 생길 때마다 똑같은 동작을 하는 메소드를 10번 오버로딩해야 하는 끔찍한 상황이 발생한다.

1. **배열 묶기** : 서로 다른 자식 객체들을 하나의 부모 타입 배열로 묶어서 일괄적으로 관리(반복문 등)할 수 있다.
2. **메소드 매개변수 단일화** : 여러 자식 타입별로 메소드를 일일이 오버로딩할 필요 없이, **부모 타입 매개변수 하나만 선언**하면 암시적 형변환을 통해 모든 자식 객체를 하나의 메소드로 인수로 전달받을 수 있다.

```csharp
class Computer { public void Shutdown() { Console.WriteLine("컴퓨터 종료"); } }
class Notebook : Computer { }
class Desktop : Computer { }

class DeviceManager
{
    // 자식 클래스(Notebook, Desktop)별로 오버로딩할 필요 없이 부모 타입(Computer) 하나로 모두 받음
    public void TurnOff(Computer device)
    {
        device.Shutdown();
    }
}

static void Main()
{
    // 1. 암시적 형변환과 명시적 형변환
    Notebook note = new Notebook();
    Computer pc = note; // 자식 -> 부모 (암시적 형변환)
    Notebook note2 = (Notebook)pc; // 부모 -> 자식 (명시적 형변환)

    // 2. 형변환의 활용 (배열 묶기)
    // 서로 다른 자식 객체들을 부모(Computer) 타입 배열 하나에 묶어서 저장 가능
    Computer[] machines = new Computer[] { new Notebook(), new Desktop() };

    DeviceManager manager = new DeviceManager();

    // 3. 형변환의 활용 (메소드 단일화 적용)
    foreach (Computer device in machines)
    {
        // Notebook이든 Desktop이든 Computer 타입으로 자동 암시적 형변환되어 정상 처리됨
        manager.TurnOff(device);
    }
}
```

<br>

## 4. 형식 변환과 `is` / `as` 연산자 (다운캐스팅의 안전망)

- **업캐스팅 (Upcasting)** : 자식 클래스의 인스턴스를 부모 클래스 형식의 변수에 담는 것. 컴파일러가 암시적으로(자동으로) 안전하게 수행한다.
- **다운캐스팅 (Downcasting)** : 부모 클래스 형식으로 업캐스팅되어 있던 변수를 다시 본래의 자식 형식으로 강제 변환하는 것. 실행 중(Runtime)에 해당 객체가 실제로 그 자식 타입이 맞는지 보장할 수 없으므로 런타임 에러(`InvalidCastException`)가 발생할 위험이 크다.

- **안전한 형 변환 연산자 (C# 고유 기능)**
  - **`is` 연산자** : 객체가 해당 타입과 호환되는지 검사하여 `true` / `false`를 반환한다.
  - **`as` 연산자 (권장)** : 형 변환을 시도하고, 만약 실패하면 예외를 던지는 대신 **`null`을 반환**한다. (단, 값 형식에는 사용할 수 없고 참조 형식에만 사용 가능). 강제 캐스팅 `(Type)obj` 보다 훨씬 안전하므로 모던 C#에서는 `as` 연산자 사용을 강력히 권장한다.

```csharp
class Mammal { }
class Dog : Mammal { public void Bark() { } }
class Cat : Mammal { public void Meow() { } }

static void Main()
{
    Mammal mammal = new Dog(); // 안전한 업캐스팅 (자동)

    // 1. 강제 다운캐스팅 (위험)
    // Cat cat = (Cat)mammal; // 런타임 에러 발생! (실제로는 Dog 객체이므로 프로그램 강제 종료)

    // 2. is 연산자를 통한 안전한 검사
    if (mammal is Dog)
    {
        Dog d = (Dog)mammal;
        d.Bark();
    }

    // 3. as 연산자를 통한 우아한 형 변환 (C# 권장 방식)
    Cat c = mammal as Cat;
    if (c != null) // 변환 실패 시 c에는 null이 담김
    {
        c.Meow();
    }
    else
    {
        Console.WriteLine("Cat으로 변환할 수 없습니다."); // 이 구문이 안전하게 실행됨
    }
}
```

- C#에서 정의되는 모든 클래스의 부모는 `object`이다.

```csharp
Computer computer = new Computer();
object obj1 = computer;
Computer pc1 = obj1 as Computer;

Notebook notebook = new Notebook();
object obj2 = notebook;
Notebook pc2 = obj2 as Notebook;
```

<br>

## 5. 다형성(Polymorphism): 오버라이딩 vs 메서드 숨기기

업캐스팅된 상태에서도 부모의 메소드가 아닌, 객체 본연(자식)의 재정의된 메소드가 실행되게 만드는 객체지향의 꽃이다.

- **메소드 오버라이딩 (Overriding) - 다형성 구현**
  - 기반 클래스에서 메소드를 선언할 때 **`virtual`** 키워드를 붙여 재정의를 허용한다.
  - 파생 클래스에서는 **`override`** 키워드를 붙여 부모의 메소드를 덮어쓴다.
  - **동적 바인딩(Dynamic Binding)**: 변수의 껍데기(타입)가 부모 타입이더라도, 그 안에 든 **실제 객체의 타입**을 실행 시간(Runtime)에 검사하여 자식의 재정의된 메소드를 정확히 찾아 호출한다.
- **메소드 숨기기 (`new` 한정자)**
  - 파생 클래스에서 `new` 키워드로 메소드를 만들면, 부모의 메소드를 덮어쓰지 않고 완전히 별개의 새로운 메소드를 만들면서 부모의 메소드를 '숨김(Hiding)' 처리한다.
  - **정적 바인딩(Static Binding)**: 컴파일러는 객체의 실제 타입은 무시하고, **변수의 껍데기(타입)**만 보고 호출할 메소드를 결정한다.

```csharp
class Base
{
    public virtual void MyMethod() { Console.WriteLine("Base.MyMethod()"); }
    public void NormalMethod() { Console.WriteLine("Base.NormalMethod()"); }
}

class Derived : Base
{
    // 1. 오버라이딩 (다형성 작동)
    public override void MyMethod() { Console.WriteLine("Derived.MyMethod()"); }

    // 2. 메소드 숨기기 (다형성 작동안함)
    public new void NormalMethod() { Console.WriteLine("Derived.NormalMethod()"); }
}

static void Main()
{
    Base obj = new Derived(); // 업캐스팅: 변수는 Base 타입이지만 실제 알맹이는 Derived 객체

    // virtual - override : 실제 알맹이(Derived)를 찾아감 (동적 바인딩)
    obj.MyMethod(); // 출력: Derived.MyMethod()

    // 일반 - new : 알맹이는 무시하고 변수의 껍데기(Base)만 보고 판단함 (정적 바인딩)
    obj.NormalMethod(); // 출력: Base.NormalMethod()
}
```

- 오버라이딩 제한: `sealed` 키워드
  `virtual` 키워드가 붙어 다형성이 허용된 메소드라도, 상속이 계속 깊어지다 보면 어느 지점부터는 "**더 이상 자식들이 이 메소드를 마음대로 뜯어고치지 못하게 막아야겠다**"라고 결정해야 할 때가 있다. 이때 `sealed` 키워드를 사용한다.
  - 파생 클래스에서 부모의 메소드를 오버라이딩할 때 `sealed override` 형태로 함께 명시한다.
  - 이렇게 봉인된 메소드는, 해당 클래스를 상속받는 또 다른 하위 클래스에서 절대 다시 오버라이딩(`override`)할 수 없게 된다.

```csharp
class Base
{
    public virtual void SealMe() { Console.WriteLine("Base"); }
}

class Derived : Base
{
    // 오버라이딩을 하되, 여기서부터 더 이상 재정의하지 못하게 '봉인(sealed)'함
    public sealed override void SealMe() { Console.WriteLine("Derived"); }
}

class WantToOverride : Derived
{
    // 컴파일 에러 발생!
    // 부모인 Derived에서 SealMe() 메소드를 sealed로 봉인했기 때문에 더 이상 오버라이딩할 수 없음
    public override void SealMe() { Console.WriteLine("WantToOverride"); }
}
```

<br>

## 6. 연산자 오버로딩 (Operator Overloading)

프로그래머가 직접 만든 클래스나 구조체(사용자 정의 타입)에 대해 덧셈(`+`), 뺄셈(`-`), 동등 비교(`==`) 같은 기본 연산자가 어떻게 동작할지 정의하는 기능이다.

- **선언 방식**: `public static 반환타입 operator 연산자(매개변수1, 매개변수2)`
- 반드시 `public`과 `static` 한정자를 동시에 사용해야만 컴파일러가 인식할 수 있다.

```csharp
public class Kilogram
{
    public double mass;
    public Kilogram(double value) { this.mass = value; }

    // + 연산자 오버로딩 정의
    public static Kilogram operator +(Kilogram a, Kilogram b)
    {
        // 내부 mass 값을 더해 새로운 Kilogram 객체를 반환
        return new Kilogram(a.mass + b.mass);
    }
}

static void Main()
{
    Kilogram kg1 = new Kilogram(5);
    Kilogram kg2 = new Kilogram(10);

    // 오버로딩된 + 연산자가 작동하여 깔끔하게 객체 간 덧셈 수행 가능
    Kilogram kg3 = kg1 + kg2;
    Console.WriteLine(kg3.mass); // 출력: 15
}
```
