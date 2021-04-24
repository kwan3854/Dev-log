# 1장 출생

## 1.1 -er로 끝나는 이름을 사용하지 마세요.

객체는 자신의 **가시성 범위(scope of visibility)** 안에서 살아간다.

- 객체와 클래스의 차이

  클래스는 객체의 팩토리(factory) 이다. 클래스는 객체를 생성한다.

  이를 클래스가 객체를 인스턴스화한다 라고 표현.

new 를 통해 객체를 생성 가능하다. Java등의 발전된 언어는 delete 지원하지 않는다. c++은 지원함.

클래스를 객체의 능동적인 관리자로 생각해야 한다.

클래스는 객체를 꺼내거나 반환할 수 있는 위치이기 때문에, 클래스를 저장소 또는 웨어하우스라고 불러야 한다.

---

클래스 이름을 짓는 적절한 방법

클래스의 이름은 무엇을 하는지가 아니라 무엇인지에 기반해야 한다.

객체는 그의 역량으로 특정지어야 한다. 속성이 아니라 할 수 있는 일로 설명해야 한다.

객체는 연결장치도 아니고, 절차의 집합이 아니다.

객체는 캡슐화된 데이터의 대표자이다.

따라서 클래스의 이름은 -er로 끝나면 안된다.

---

예) 소수를 찾는 알고리즘 클래스의 이름

- 잘못된: Primer, PrimeFinder, PrimeChooser...
- 올바른: PrimeNumbers

마치 어떤 절차가 안에 있는게 아니라 그냥 소수들의 집합이 있는것과 같이 대하라.

객체는 그 소수들의 집합, 목록 그 자체여야 한다.

---

요약

새로운 클래스에 이름을 붙일 때는 무엇을 하는지가 아니라 무엇인지를 생각해야 한다.

---

## 1.2 생성자 하나를 주 생성자로 만드세요

C++ 에서는 생성자를 ctor라고 부른다.

이 책이 권장하는대로 클래스를 설계한다면, **ctor의 갯수가 메서드의 수보다 많아진다.**

**핵심은 응집도가 높고 견고한 클래스에는 적은 수의 메서드와 상대적으로 더 많은 수의 ctor가 존재한다는 점이다.**

여러 타입을 수용할 수 있도록 오버로딩해서 구현.

하나의 주(primary) ctor 와 여러개의 부(secondary) ctor 로 구분한다.

- 주 ctor: 초기화 로직을 구현
- 부 ctor: 주 ctor를 호출하도록 구현.
- 부 ctor를 먼저 위치시키고 주 ctor를 나중에 위치시킨다.

이 로직의 핵심은

**중복 코드를 방지하고 설계를 더 간결하게 만들기 때문에 유지보수성이 향상된다** 는 점이다.

---

메서드 오버로딩을 지원하지 않는 언어에서는?

인자들을 map에 담아 전달하는 방법.

---

내부 프로퍼티는 오직 한 곳에서만 초기화해야 한다는 핵심 원칙만 따르면 된다.

나머지 부분에서는 단순히 인자를 준비하고, 포메팅하고, 파싱하고, 변환만 해야 한다.

---

복잡성과 중복이라는 두 문제가 해결되었다.

---

## 1.3 생성자에 코드를 넣지 마세요

생성자에서 인자들을 어떻게 다뤄야 할까?

인자에 손대지 말라!

---

진정한 객체지향에서 인스턴스화란 더 작은 객체들을 조합해서 더 큰 객체를 만드는 것을 의미

객체를 생성하는 작업과 객체가 우리를 위해 작업을 하게 만드는 것을 분리

---

성능관점

만약 객체가 생성될때마다 ctor에서 바로 파싱이되면 최적화 못함

파싱 부분 분리 해놓으면 최적화 가능

요청이 있을 때 (on demand)로 파싱하고, 여러번 파싱 안되게 데코레이터(decorator)를 추가해서 최초의 파싱 결과를 캐싱할 수도 있음.

---

객체는 요청을 받을 때만 행동하고 그 전에는 어떤 일도 하지 않아야 한다.

객체들은 좋은 방식으로 매우 게을러야 한다.

가벼운 ctor은 설정하기 쉽고 투명하게 사용할 수 있기 때문에 객체를 더 빠르게 만들 수 있다.

---

# 2. 학습

이번 챕터에서 배우게 될 교훈을 한 문장으로 말한다면

> 객체는 작아야 한다

---

## 2.1 가능하면 적게 캡슐화하세요

복잡성은 직접적으로 유지보수성에 영향을 미친다.

4개 또는 그 이하의 객체를 캡슐화 할 것을 권장.

그 이상이 필요하면 클래스에 문제가 있는 것.

```java
class Cash {
  private Integer digits;
  private Integer cents;
  private String currency;
}
```

위 클래스는 3개의 객체를 캡슐화하고 있다.

이 3개의 객체들이 모여 Cash 클래스의 객체를 식별한다.

위 3개 객체가 같은 값으로 모여 하나의 클래스가 되면 두 객체는 서로 동일하다.

---

상태 없는 객체는 존재해서는 안되고, 상태는 객체의 식별자여야 한다.

---

4개 이상의 좌표는 직관에 위배된다.

우리의 사고방식으로 4개 이상의 요소로 구성된 좌표를 이해하는것은 너무나도 어려움

개념을 트리 형태로 구현해서 4개이하로 유지해야 함.

---

## 2.2 최소한 뭔가는 캡슐화 하세요

또 다른 극단에는 어떤 것도 캡슐화 하지 않는 객체가 존재한ㄷ나.

```java
class Year {
  int read() {
    return System.currentTimeMillis()
      / (1000 * 60 * 60 * 24 * 30 * 12) - 1970;
  }
}
```

Year 클래스의 인스턴스는 어떤 것도 캡슐화 하지 않기 때문에 이 클래스의 모든 객체들은 동일하다.

프로퍼티가 없는 클래스는 객체지향 프로그래밍에서 악명이 높은 정적 메서드와 유사하다.

실행으로부터 인스턴스 생성을 고립시켜야 한다.

오직 ctor에서만 new 연산자를 허용해야 한다.

```java
class Year {
  private Millis millis;
  Year(Millis msec) {
    this.millis = msec;
  }
  int read() {
    return this.millis.read()
      / (1000 * 60 * 60 * 24 * 30 * 12) - 1970;
  }
}
```

무언가는 캡슐화 해야한다.

---

## 2.3 항상 인터페이스를 사용하세요

객체들은 서로를 필요로 하기 때문에 결합한다.

객체 사이의 강한 결합도가 심각한 문제가 된다.

최선을 다해서 객체를 분리해야 한다.

이를 가능케 하는게 인터페이스이다.

----

```java
interface Cash {
  Cash multiply(float factor);
}
```

```java
class DefaultCash implements Cash {
  private int dollars;
  DefaultCash(int dlr) {
    this.dolloars = dlr;
  }
  @Override
  Cash multiply(float factor) {
    return new DefaultCash(this.dolloars * factor);
  }
}
```

```java
class Employee {
  private Cash salary;
}
```

Employee 클래스는 Cash 인터페이스의 구현 방법에 아무런 관심이 없다.

---

클래스 안의 **모든** 퍼블릿 메서드가 인터페이스를 구현하도록 만들어야 한다.

즉 다음과 같이 해서는 안된다.

```java
class Cash {
  public int cents() {
    // 어떤 작업을 수행한다.
  }
}
```

 cents() 메서드는 어떤 것도 오버라이드 하지 않기 떄문에 문제가 있다.

이 설계는 클래스의 사용자(다른 클래스)로 하여금 이 클래스에 강하게 결합되도록 조장한다.

---

**느슨한 결합도**

---

## 2.4 메서드 이름을 신중하게 선택하세요

- 빌더의 이름은 명사로 지어라.
- 조정자의 이름은 동사로 지어라.

---

- 빌더: 뭔가를 만들고 새로운 객체를 반환하는 메서드
- 조정자: 객체로 추상화한 실세계 엔티티를 수정하는 메서드를 조정자라고 함.

---

```java
int pow(int base, int power);
float speed();
Employee employee(int id);
String parsedCell(int x, int y);
```

```java
void save(String content);
void put(String key, Float value);
void remove(Employee emp);
void quicklyPrint(int id);
```

quickly는 단순히 print를 설명하는 것일 뿐이다.

핵심은 print라는 동사.

---

빌더와 조정자 사이에는 어떤 메서드도 존재해서는 안된다.

즉, 뭔가를 조작한 수 반환하거나, 뭔가를 만드는 동시에 조작하는 메서드가 있어서는 안된다.

잘못된 예)

```java
// 저장된 전체 바이트를 반환
int save(String content);
// 조정자이기 때문에 이 이름은 올바르지 않다.
// void를 반환하도록 바꾸거나, bytesSaved()와 같은 이름으로 바꿔야 함.

// map이 변경된 경우 TRUE를 반환
boolean put(String key, Float value);
// 조정자 처럼 동작하지만 빌더처럼 boolean을 반환하기 때문에 동일한 문제 발생
// 아무것도 반환하지 않도록 수정해야 함.
// 안에 success() 메서드 만들어서 성공/실패 여부 반환하는 빌더 포함시켜야 함.

// speed를 저장한 후 이전 값을 반환
float speed(float val);
// 빌더인 동시에 조정자임.
// SaveSpeed 클래스를 추가하고 speed를 저장하는 메서드와 이전 값을 반환하는 메서드를 구현해야 함.
```

getter 와 setter는 동사로 시작하지만, getter는 근본적으로 어떤 값을 반환하는 빌더이다.

따라서 getter에 반대한다.

---

### 2.4.1 빌더는 명사다

어떤 것을 반환하는 메서드의 이름을 동사로 짓는 것은 잘못이다.

제과점에가서

>  브라우니를 요리해 주세요, 커피 한잔 끓여 주세요

하지 않는다.

>  브라우니 주세요, 커피 한잔 주세요 라고 한다.

---

```java
class Bakery {
  Food cookBrownie();
  Drink brewCupOfCoffee(String flavor);
}
```

두 메서드는 실제로는 객체의 메서드가 아니다. 이들은 프로시저이다.

이는 객체지향적인 접근 방법이 아니라 절차적인 접근방법이다.

```java
Food* cook_brownie() {
  // 브라우니를 요리해서
  // 반환한다
}
Drink* brew_cup_of_coffee(char* flavor) {
  // 커피를 끓여서
  // 반환한다
}
```

이 코드에는 제과점이라는 개념이 전혀 드러나 있지 않다.

프로시저에 불과하다.

**객체는 자신의 의무를 수행하는 방법을 알고 있고 존중 받기를 원하는 살아있는 유기체이다.**

객체는 단순히 지시에 따르는 것이 아니라 계약에 기반해 일하고 싶어한다.

---

메서드의 이름을 동사로 지을 때에는 객체에게 무엇을 할 지 를 알려주어야 한다.

객체에게 무엇을 만들라고 요청하는 것은 존중이 없는 방식이다.

무엇을 만들어야 하는지만 요청하고, 만드는 방법은 객체 스스로 결정하도록 해야 한다.

---

다음의 메서드 이름은 모두 잘못지어졌다.

```java
InputStream load(URL url);
String read(File file);
int add(int x, int y);
```

다음과 같이 고쳐야 한다.

```java
InputStream stream(URL url);
String content(File file);
int sum(int x, int y);
```

우리는 객체에게 x와 y를 더하라고(add) 요청하지 않는다.

대신, 두 수의 합(sum)을 계산하고 새로운 객체를 반환해 달라고 요청한다.

---

### 2.4.2 조정자는 동사다

실세계의 엔티티를 조작해야 하는 경우에는, 다음과 같이 객체가 그 작업을 수행하도록 요청한다.

```java
class Pixel {
  void paint(Color color);
}
Pixel center = new Pixel(50, 50);
center.paint(new Color("red"));
```

paint() 메서드는 값을 반환하지 않는다.

우리는 바텐더에게 음악을 틀고 현재의 볼륨 상태를 말해주세요 하지 않는다.

---

오직 빌더만이 값을 반환할 수 있고 이름은 명사이다.

객체가 뭔가를 조정해야 한다면 이름은 동사이고 반환값이 없다.

---

빌더 패턴은 유지보수성이 낮고 응집도가 떨어지는 더 커다란 객체를 만들도록 조장하기 때문에 빌더 패턴에 반대한다.

빌더 패턴을 사용하는 대신 복잡한 객체를 더 작은 객체들로 나눠야 한다.

빌더 패턴을 쓰지 마라.

---

## 2.4.3 빌더와 조정자 혼합하기

파일 내용을 저장하고 저장된 바이트 수를 반환하는 메서드가 있다고 가정

```java
class Document {
  int write(InputStream content);
}
```

이 메서드는 앞서말한 원칙을 위반하고 있음.

메서드 write의 반환 타입을 void로 바꿔야 하지만, 우리는 실제로 저장된 바이트 수를 알 필요가 있음.

하나의 메서드 안에서 너무 복잡한 일을 처리하고 있음.

다음과 같이 리팩토링 하는것을 추천.

```java
class Document {
  OutputPipe output();
}
class OutputPipe {
  void wrtie(InputStream content);
  int bytes();
  long time();
}
```

output() 메서드는 빌더이다.

OOP의 전체 목적은 개념을 고립시켜 복잡성을 낮추는 것이다.

고립시킨 개념이 작을수록 이해하고 유지보수하기도 더 쉬워진다.

이 경우 고립시킨 개념은 '문서에 바이트 쓰기'이다.

---

## 2.4.4 Boolean 값을 결과로 반환하는 경우

Boolean 값을 반환하는 메서드의 이름은 어떻게 짓는 것이 좋을까?

지금까지 원칙에 의하면 isEmpty(), equals(), exists() 다 틀렸다.

그러나 Boolean 값을 반환하는 메서드는 규칙에 있어서 예외적인 경우이다.

빌더에 속하지만, 가독성 측면에서 이름은 **형용사** 로 지어야 한다.

---

메서드를 읽을때는 is를 앞에 붙여서 읽고

실제로는 is를 때고 이름을 지어라.

---

```java
boolean empty(); // is empty
boolean readable(); //is readable
boolean negative(); // is negative
```

---

그러나 다음과 같은 상황에서는 문제가 생긴다.

```java
boolean equals(Object obj); //isEquals?
boolean exists(); //isExists?
// 앞에 is를 붙이면 올바르지 않은 문장이 됨
```

equalTo, present 로 바꾸면 됨.

---

## 2.5 퍼블릭 상수(Public Constant)를 사용하지 마세요

객체들은 어떤 것도 공유해서는 안된다.

대신 독립적이어야 하고 닫혀 있어야 한다.

상수를 이용한 공유 매커니즘은 캡슐화와 객체지향적인 사고 전체를 부정하는 일이다.

결합도가 높아지고 응집도가 낮아진다.

---

### 2.5.1 결합도 증가

```java
class Records {
  void write(Writer out) {
    for (Record rec : this.all) {
      out.write(rec.toString());
      out.write(Constants.EOL); // 여기!
    }
  }
}
```

```java
class Rows {
  void print(PrintStream pnt) [
    for (Row row : this.fetch()) {
      pnt.printf(
      "{%s}", row, Constants.EOL) // 여기!
     );
    }
  ]
}
```

두 클래스는 모두 같은 객체에 의존하고 있으며, 이 의존성은 **하드 코딩** 되어 있다.

의존성을 쉽게 분리할 수 있는 방법이 없다.

---

### 2.5.2 응집도 저하

낮은 응집도는 객체가 자신의 문제를 해결하는데 덜 집중한다는 것을 의미한다.

객체들은 상수를 다루는 방법을 알고 있어야 한다.

객체는 아주 멍청한 상수 위에 자신만의 **의미론** 을 덧붙여야 한다.

---

객체 사이에 데이터를 중복해서는 안된다.

대신, 기능을 공유할 수 있도록 **새로운 클래스** 를 만들어야 한다.

**데이터가 아니라 기능을 공유해야 한다**

---

## 2.6 불변 객체로 만드세요

모든 클래스를 상태 변경이 불가능한 불변 클래스 (immutable class) 로 구현하면 유지보수성을 크게 향상시킬 수 있다.

예를 들어 다음 Cash 클래스를 이용해서 생성한 객체는 상태 변경이 가능하기 떄문에 가변 객체라고 부른다.

```java
class Cash {
  private int dollars;
  
  public void stDollars(int val) {
    this.dollars = val;
  }
}
```

다음 클래스는 앞의 Cash와 비슷하지만, 상태를 변경할 수 없는 불변 객체를 생성한다.

```java
class Cash {
  private final int dollars;
  Cash(int val) {
    this.dollars = val;
  }
}
```

불변 객체는 필요한 모든 것을 내부에 캡슐화하고 변경할 수 있도록 통제한다.

불변 객체를 수정해야 한다면 프로퍼티를 수정하는 대신 새로운 객체를 생성해야 한다.

예를 들어 Cash 클래스에 금액을 곱하는 연산자를 추가하면 가변클래스라면,

```java
class Cash {
  private int dollars;
  public void mul(int factor) {
    this.dolloars *= factor;
  }
}
```

같은 작업을 불변 클래스에 하면

```java
class Cash {
  private final int dollars;
  public Cash mul(int factor) {
    return new Cash(this.dollars * factor);
  }
}
```

불변 객체는 어떤 방식으로든 자기 자신을 수정할 수 없다. 항상 원하는 상태를 가지는 새로운 객체를 생성해서 반환해야 한다.

위 코드를 사용할때는, 가변 객체의 경우

```java
Cash five = new Cash(5);
five.mul(10);
System.out.print(five); // "$50"가 출력될 것이다.
```

불변 객체를 이용하면

```java
Cash five = new Cash(5);
Cash fifty = five.mul(10);
System.out.println(fifty); // "$50"가 출력될 것이다.
```

가변 객체는 전반적인 객체 패더다임의 오용이다. 항상 불변 객체를 사용해야 한다.

가변 객체는 존재해서는 안된다. 가변 객체의 사용을 **엄격하게 금지해야 한다.**

어떤 이는 '지연 로딩(lazy loading)'을 구현하려면 Java, Ruby, C++ 같은 언어들에서는 불변 객체를 이용해서는 불가능 하다고 주장하는데, 캐싱 매커니즘으로 구현할 수 있다. 그러나 언어 차원에서 지연 로딩을 지원할 필요가 있는것은 사실이다.

---

### 2.6.1 식별자 가변성 (Identity Mutability)

불변 객체에는 식별자 가변성 문제가 없다.

간단히 말해, 이 문제는 동일해 보이는 두 객체를 비교한 후 한 객체의 상태를 변경할 때 수면 위로 떠오른다. 두 객체가 더 이상 동일하지 않지만, 여전히 두 객체가 동일하다고 생각할 수 있다. 그 반대일 수도 있고.

```java
Map<Cash, String> map = new HashMap<>();
Cash five = new Cash("$5");
Cash ten = new Cash("$10");
map.put(five, "five");
map.put(ten, "ten");
five.mul(2);
System.out.println(map); // {$10=>"five", $10=>"ten"}
```

five.mul(2)를 수행해서 five의 상태를 변경하면 map은 더 이상 올바르지 않다. map 안에 동일한 키가 두 개 존재하게 된다.

처음에 five와 ten이라는 **동일하지 않은** 두 객체를 생성했다. 그리고 나서 두 객체를 map에 추가하는데, 이때 사용하는 키가 서로 다르기 때문에 HashMap은 두 개의 독립적인 엔트리를 생성한다. 이후 five객체를 변경했지만, map에 이런 사실을 알려주지 않았기 때문에 map은 변경이 일어났다는 사실을 전혀 인식하지 못했다.

결과적으로 매우 혼란스러운 상태의 map이 남게 되었다.

```java
map.get(five); // ten 과 five 중 하나가 반환될 것이다.
```

이 문제는 식별자 가변성으로 알려져 있다. 매우 심각하고 찾기 어려운 버그로 이어질 수 있다.

불변 객체를 사용하면 객체를 map에 추가한 후에는 상태 변경이 불가능하기 때문에 식별자 가변성 문제가 발생하지 않는다.

---

### 2.6.2 실패 원자성 (Failure Atomicity)

불변 객체를 이용해서 얻을 수 있는 또 다른 장점으로 실패 원자성이 있다.

실패 원자성이란 완전하고 견고한 상태의 객체를 가지거나 아니면 실패하거나 둘 중 하나만 가능한 특성이다.

가변 클래스 Cash 예시를 다시 보자.

```java
class Cash {
  private int dollars;
  private int cents;
  public void mul(int factor) {
    this.dollars *= factor;
    if (/* 뭔가 잘못 됐다면 */) {
      throw new RuntimeException("oops...");
    }
    this.cents *= factor;
  }
}
```

mul() 메서드를 실행하는 도중에 예외가 던져지면 객체의 절반(this.dollars)만 수정되고 나머지 절반(this.cents)은 원래 값을 유지한다.

이로 인해 매우 심각하고 발견하기 어려운 버그가 발생할 수 있다.

불변객체는 내부의 어떤 것도 수정할 수 없기 때문에 이런 결함이 발생하지 않는다.

```java
class Cash {
  private final int dollars;
  private final int cents;
  public Cash mul (int factor) {
    if (/* 뭔가 잘못 됐다면 */) {
      throw new RuntimeException("oops...");
    }
    return new Cash(this.dollars * factor, this.cents * factor);
  }
}
```

가변 객체를 사용하더라도 실패 원자성을 구현 할 수 있지만, 특별한 주의를 기울여야 가능하다.

반면, 불변 객체를 사용하면 별도의 처리 없이 자동적으로 원자성을 얻을 수 있다.

만약 가변 객체 안에서 명시적으로 실패 원자성을 보장할려고 하면 어떤 단점이 있을까?

바로 객체의 복잡성이 훨씬 더 높아지고, 실수할 가능성이 더 커진다는 점이 있다.

---

### 2.6.3 시간적 결합 (Temporal Coupling)

불변 객체를 사용해서 얻을 수 있는 또 다른 장접은 시간적 결합을 제거 할 수 있다는 점이다.

```java
Cash price = new cash();
price.setDollars(29);
price.setCents(95);
System.out.println(price); // "$29.95"
```

먼저 모든 private 프로퍼티가 NULL 값을 가지도록 골격을 만든다(인스턴스화). 그리고 나서 setter를 이용해 프로퍼티 값들을 설정한다(초기화)

진정한 객체 사고의 관점에서는 완전히 잘못된 방식이다.

위 예시의 4줄의 코드의 각 줄은 특정한 순서로 정렬되어 있다. 각 줄은 시간적인 순서에 따라 서로 결합되어 있다.

만약 실수로 다음과 같이 코드를 재정렬하더라도 컴파일은 여전히 성공한다.

```java
Cash price = new Cash();
price.setDollars(29);
System.out.println(price); // "29.00"!
price.setCents(95);
```

이런 상황에서 컴파일러는 아무런 도움도 되지 않는다.

어떤 줄이 어떤 줄 앞에 있고 뒤에 있어야 하는지는 온전히 프로그래머의 몫이라는 뜻이다.

이 순서들을 일일이 기억해야 한다면 **유지보수** 에 있어 어려움이 크다.

```java
Cash price = new Cash();
// x 를 계산하는 50줄 이상의 코드
price.setDollars(x);
// y 를 계산하는 30줄 이상의 코드
price.setCents(y);
// 다른 일을 수행하는 25줄의 코드
System.out.println(price);
```

setter들의 실행 순서를 유지하면서 println() 이전에 실행돼야 한다는 사실을 이해하는것은 절대로 쉬운 일이 아니다.

불변 객체를 이용하면 이를 해결할 수 있다.

```java
Cash price = new Cash(29, 95);
System.out.println(price); // "$29.95"
```

이 경우 **인스턴스화** 와 **초기화** 를 분리시킬 수 없다.

코드가 컴파일 되지 않기 때문에, 객체 초기화와 println()의 순서를 변경할 수도 없다.

---

### 2.6.4 부수효과 제거(side effect-free)

객체가 가변적이면 기본적으로 누구든 쉽게 객체를 수정할 수 있다.

다음 예시에서 화면을 출력하는 메서드에 price 객체를 전달한다고 해보자.

이 메서드를 작성한 개발자는 price 객체의 값을 출력할 뿐 아니라 가격을 2배로 증가시키는 실수를 저질렀다.

```java
void print(Cash price) {
  System.out.println("Today price is: " + price);
  price.mul(2);
  System.out.println("Buy now, tomorrow price is: " + price);
}
```

이제 이 메서드를 호출하면 부수효과가 발생한다.

```java
Cash five = new Cash(5);
print(five);
System.out.println(five); // "$10", 이런...
```

문제가 발생한 지점을 찾기 위해 five 객체를 수정한 모든 곳을 디버깅해야 한다.

반면 Cash 클래스를 불변으로 만들면 어떤 누구도 객체를 수정할 수 없다.

---

### 2.6.5 NULL 참조 없애기

OOP 에서는 NULL 을 피해야 한다. 이는 4.1과 3.3 에서 더 자세히 본다.

여기에서는 unset 프로퍼티에 관해서만 이야기한다.

```java
class User {
  private final int id;
  private String name = null;
  public User(int num) {
    this.id = num;
  }
  public void setName(String txt) {
    this.name = txt;
  }
}
```

이 클래스의 인스턴스가 생성될 때 name 프로퍼티의 값으로 NULL이 할당된다.

이 값은 나중에 setName() 메서드를 호출할 때 초기화되고, 그 전까지는 값이 NULL 인 상태를 유지한다.

값에 접근하기 전에 NULL 인지 확인해야 하고, 코드가 `if name != null` 로 가득 찰 것이다.

자칫 잘못해서 NULL 체크를 잊어버리면 NullPointerException이나 세그멘테이션 오류와 마주할 것이다.

더 큰 문제는 **유지보수성** 이다.

언제 객체가 유효한 상태이고 언제 **객체가 아닌 다른 형태** 로 바뀌는지를 이해하기가 어렵다.

**초기화 되지 않은 name 프로퍼티를 가지고 있는 User 객체가 필요할까?**

아마 다른 클래스가 필요하지만 새로운 클래스를 생성하는 작업이 귀찮을때 이런 문제가 발생할 것이다.

**NULL 참조를 없애면 작고, 견고하고, 응집도 높은 객체를 생성할 수 밖에 없도록 강제되기 때문에 결과적으로 유지보수하기 쉬운 객체를 만든다.**

---

### 2.6.6 스레드 안전성 (Thread Safety)

스레드 안전성이란 글자 그대로 객체가 여러 스레드에서 동시에(concurrently) 사용될 수 있으며 그 결과를 항상 **예측 가능하도록** 유지할 수 있는 객체의 품질을 의미한다.

다음은 스레드에 안전하지 않은 인스턴스를 생성하는 클래스의 예이다.

```java
class Cash {
  private int dollars;
  private int cents;
  public void mul(int factor) {
    this.dollars *= factor;
    this.cents *= factor;
  }
}
```

얼핏 보기에는 결함이 없어 보이지만, 두 개의 병렬 스레드 안에서 이 객체를 실행하면

```java
Cash price = new Cash("$15.10");
// 두 스레드 안에서 다음 두 줄을 실행
price.mul(2);
// "$30.20"와 "$60.40"이 예상됨
System.out.println(price);
```

직접 실행해 보면 매번 다른 숫자가 출력된다는 것을 확인할 수 있다.

가끔 "$60.20" 이라는 이상한 값이 출력된다.

첫번째 스레드가 달러와 센트에 각각 2를 곱한다.

동시에 실행되는 다른 스레드가 달러에 2를 곱하고 센트에 미처 2를 곱하지 못한 시점에 첫번째 스레드가 println() 메서드를 실행한다.

이 경우 println() 은 $60.20을 출력한다.

이와 같은 병행성 이슈는 발견하고, 디버깅하고, 해결하기 가장 어려운 문제 중 하나이다.

재현이 매우 어렵기 때문이다.

불변 객체는 실행 시점에 상태를 수정할 수 없게 금지함으로써 이 문제는 완벽히 해결한다.

어떤 스레드도 객체의 상태를 수정할 수 없기 때문에 아무리 많은 스레드가 객체에 접근해도 문제가 없다.

> 명시적인 동기화를 이용하면 정상적으로 동작은 하겠지만,
>
> 1. 가변 클래스에 스레드 안전성을 추가하는 일이 쉽지 않다.
> 2. 동기화 로직을 추가하는 일은 성능상의 비용을 초래한다.
> 3. 데드락이 발생할 수 있다.
>
> 따라서 가변 객체를 피하고 불변 객체를 이용할 것을 주장한다.

---

### 2.6.7 더 작고 단순한 객체

단순성은 유지보수성으로 해석할 수 있다.

객체가 더 단순해질 수록 응집도는 더 높아지고, 유지보수하기는 더 쉬워진다.

이해하고, 수정하고, 문서화하고, 지원하고, 리팩토링 하기 쉬운 소프트웨어가 최고의 소프트웨어이다.

Java에서 허용 가능한 클래스의 최대 크기는 주석과 공백을 포함해서 **250줄 정도라고 생각한다.** 이를 넘으면 리팩토링이 필요하다.

어플리케이션에 포함되어 있는 모든 클래스의 길이를 250줄 이하로 유지할 수만 있다면, 여러분 자신을 좋은 소프트웨어 개발자이자 아키텍트라고 생각해도 무방하다.

불변 객체를 아주 크게 만드는 일은 불가능하기 때문에, 일반적으로 불변 객체는 가변 객체보다 더 작다.

생성자 안에서만 상태를 초기화 할 수 있기 때문에 인자를 10개씩 받는 생성자를 만들지는 않는다.

불변성은 클래스를 더 깔끔하고 더 짧게 만든다. 이것이 불변 클래스를 이용할 때 얻을 수 있는 가장 중요한 장점이다.

---

## 2.7 문서를 작성하는 대신 테스트를 만드세요

문서화는 유지보수에 있어 중요한 구성요소이다.

구체적으로 문서를 만드는 일이 중요한 것이 아니라 클래스나 메서드에 관한 추가 정보에 얼마나 쉽게 접근할 수 있는지가 중요하다.

더 읽기 쉬운 코드를 만들기 위해서는, 코드를 읽게 될 사람이 비즈니스 도메인, 프로그래밍 언어, 디자인 패턴, 알고리즘을 거의 이해하지 못하는 주니어 프로그래머라고 **가정해야** 한다.

훌륭한 프로그래머는 단순한 코드를 짠다.

```java
Employee jeff = department.employee("Jeff");
jeff.giveRaise(new Cash("$5,000"));
if (jeff.performance() < 3.5) {
  jeff.fire();
}
```

이 코드의 문서화가 필요할까? 코드 자체만으로도 의미가 명확하게 전달된다.

반면 이 코드는 어떨까?

```java
class Helper {
  int saveAndCheck(float x) { .. }
  float extract(String text) { .. }
  boolean convert(int value, boolean extra) { .. }
}
```

메서드의 이름도 형편없고(섹션 2.4), 클래스 이름도 엉망인데다(섹션 1.1), 클래스의 전반적인 설계 역시 형편없다.

분명히 문서화가 필요하다.

훌륭하고 유지보수 가능한 클래스는 문서화가 필요하지 않다는 의미이다.

좋은 클래스는 목적이 명확하고, 작고, 설계가 우아하다. 다음코드처럼

```java
class WebPage {
  String content() { .. }
  void update(String content) { .. }
}
```

따라서 코드를 문서화하는 대신 코드를 깔끔하게 만들기 바란다.

깔끔하게 만든다라는 말에는 단위 테스트도 함께 만든다는 의미가 포함되어 있다.

단위 테스트 역시 클래스의 일부로 취급해야 한다.

단위 테스트는 클래스의 일부이지 독립적인 개체가 아니다(개념적으로). (기술적으로는 단위 테스트를 별도의 파일로 구현할 수 밖에 없다)

단위 테스트가 바로 문서화이다.

---

## 2.8 모의 객체(Mock) 대신 페이크 객체(Fake)를 사용하세요

이번 주제는 테스트를 최적화하기 위한 도구인 모킹(Mocking)이다.

모킹은 테스트를 최적화하기 위한 장치이다.

다음은 자기 자신을 새로운 환율로 변환할 수 있는 Cash 클래스이다.

```java
class Cash {
  private final Exchange exchange;
  private final int cents;
  public Cash(Excahnge exch, int cnts) {
    this.exchange = exch;
    this.cents = cnts;
  }
  public Cash in(String currency) {
    return new Cash(this.exchange, this.cents * this.exchange.rate ("USD", currency));
  }
}
```

Cash 클래스는 Exchange 클래스에 의존하고, Exchange 클래스는 USD를 EUR로 변환하는 데 필요한 비율을 알고 있다.

Cash 클래스를 사용하기 위해서는 Exchange의 인스턴스를 Cash의 생성자에 전달해야 한다.

```java
Cash dollar = new Cash(new NYSE("secret"), 100);
Cash euro = dollar.in("EUR");
```

NYSE 클래스는 USD에서 EUR로 변환하기 위한 환율을 찾는 방법을 알고 있으며, 뉴욕 증권 거래소에 위치한 서버에 HTTP 요청을 전송해서 이 정보를 알아낼 것이다. 패스워드로 'secret'을 사용하고 있다는 사실을 알 수 있다.

단위 테스트를 실행할 때 마다 매번 NYSE서버에 요청을 전송하고 싶지도 않고, 'secret'이라는 패스워드가 모든 프로그래머에게 노출되는 상황 역시 원치 않는다. NYSE 서버의 개입 없이 Cash 클래스를 테스트할 수 있는 방법이 필요하다.

전통적인 접근방식은 모킹이다. NYSE를 사용하는 대신, Exchange 인터페이스에 대한 모의 객체(mock) 를 생성한 후 Cash 생성자의 인자로 사용한다.

예제에서는 모킹 라이브러리로 Mockito를 사용했다.

```java
Exchange exchange = Mockito.mock(Exchange.class);
Mockito.doReturn(1.15)
  .when(exchange)
  .rate("USD", "EUR");
Cash dollar = new Cash(exchange, 500);
Cash euro = dolloar.in("EUR");
assert "5.75".equals(euro.toString());
```

이 책의 필자는 모킹 대신 페이크 객체(fake object)를 사용할 것을 제안한다.

다음은 사용자에게 전달 될 Exchange 인터페이스의 최종 코드이다.

```java
interface Exchange {
  float rate(String origin, String target);
  final class Fake implements Exchange {
    @Override
    float rate(String origin, String target) {
      return 1.2345;
    }
  }
}
```

모킹 대신 페이크 클래스를 사용한 단위 테스트를 살펴보자

```java
Exchange exchange = new Exchange.Fake();
Cash dollar = new Cash(exchange, 500);
Cash euro = dollar.in("EUR");
assert "6.17".equals(euro.toString());
```

테스트가 훨씬 짧아 보인다.

페이크 클래스를 사용하면 테스트를 더 짧게 만들 수 있기 떄문에 유지보수성이 눈에 띄게 향상된다.

반면 모킹의 경우, 테스트가 매우 장황해지고, 이해하거나 리팩토링하기 어려워진다.

더 큰 문제는 모킹은 가정(assumption)을 사실(fact)로 전환시키기 때문에 단위 테스트를 유지보수하기 어렵게 만든다.

```java
Exchange exchange = Mockito.mock(Exchange.class);
Mockito.doReturn(1.15)
  .when(exchange)
  .rate("USD", "EUR");
```

이 코드는 글자 그대로 "Cash가 Exchange.rate()를 호출하리라고 가정한다" 고 이야기한다.

Cash 클래스는 블랙박스이기 때문에 Cash 클래스 내부에서 실제로 Exchange.rate()가 호출되는지는 정확하게 알 수 없다.

우리는 불확실한 **가정** 을 세우고 이 가정을 중심으로 전체 테스트를 구축하고 있다.

클래스의 공개된(public) 행동을 변경하지 않을 경우 단위 테스트는 **실패해서는 안된다.**

하지만 위의 예시 단위 테스트는 아무런 이유 없이도 실패할 수 있다.

Exchange 인터페이스를 다음과 같이 수정한다고 가정해보자.

```java
interface Exchange {
  float rate(String target);
  float rate(String origin, String target);
}
```

한 개의 인자를 받는 첫 번째 메서드는 USD를 target으로 환전하는데 필요한 환율을 반환하고

두 개의 인자를 받는 두 번째 메서드는 origin과 target 통화를 둘 다 지정할 수 있도록 허용한다.

이제 하나의 인자를 받는 새로운 rate 메서드를 사용하도록 Cash 클래스를 수정하면, 단위 테스트는 실패한다.

아무것도 실패하지 않았지만, 테스트는 실패했다는 잘못된 신호를 보낸다.

이런 종류의 실패는 단위 테스트에 대한 신뢰를 완전히 무너뜨린다.

대부분의 실패는 모킹 때문에 일어난다.

이제 동일한 상황에서 페이크 모킹 대신 페이크 클래스인 Exchange.Fake를 사용하면 어떻게 되는지 보자.

페이크 클래스가 존제하는 상황에서 Exchange 인터페이스를 변경하기 위해서는 자연스럽게 Exchange.Fake 클래스의 구현도 함께 변경해야 한다.

```java
interface Exchange {
  float rate(String target);
  float rate(String origin, String target);
  final calss Fake implements Exchange {
    @Override
    float rate(String target) {
      return this.rate("USD", target);
    }
    @Override
    float rate(String origin, String target) {
      return 1.2345;
    }
  }
}
```

단위 테스트를 변경할 필요가 있을까? 없다.

단위 테스트가 깨질까? 깨지지 않는다.

Cash의 행동을 변경하지 않았고, 단위 테스트는 어떠한 잘못된 신호도 보내지 않는다.

이 테스트는 훌륭한 단위 테스트이고 신뢰할 수 있다.

---

## 2.9 인터페이스를 짧게 유지하고 스마트(smart)를 사용하세요

클래스를 작게 유지하는 일이 중요하다면 인터페이스를 작게 만드는 것은 훨씬 더 중요하다.

클래스가 다수의 인터페이스를 구현하기 떄문이다.

두 개의 인터페이스가 각각 5개의 메서드를 선언하고 있다면, 두 인터페이스를 모두 구현하는 클래스는 10개의 public 메서드를 가진다.

이 클래스를 우아하다고 말하기는 어렵다.

```java
interface Exchange {
  float rate(String target);
  float rate(String source, String target);
}
```

이 인터페이스는 너무 많은 것을 **요구하기** 때문에 설계 관점에서는 형편없는 인터페이스이다.

인터페이스는 구현 클래스가 준수해야 하는 계약이다.

이런 종류의 계약은 단일 책임 원칙을 위반하는 클래스를 만들도록 부추긴다. 다시말해 응집도가 낮은 클래스를 만들게 한다.

이 계약은 거래소가 환율을 계산하도록 요구하는 동시에 클라이언트가 환율을 제공하지 않을 경우 기본 환율을 사용하도록 강제한다.

두 rate 메서드는 매우 밀접하게 연관되어 있지만 사실 두 개의 독립적인 함수이다.

하나의 인자를 받는 rate() 메서드는 이 인터페이스에 포함되어서는 안된다.

이를 해결하기 위해 또 다른 인터페이스를 정의해야 할까?

그럴 필요는 없다. 인터페이스 안에 스마트 클래스를 추가해서 해결할 수 있다.

```java
interface Exchange {
  float rate(String source, String target);
  final class Smart {
    private final Exchange origin;
    public float toUsd(String source) {
      return this.origin.rate(source, "USD");
    }
  }
}
```

이 스마트 클래스는 아주 명확하고 공통적인 작업을 수행하는 많은 메서들을 포함할 수 있다.

스마트 클래스를 인터페이스와 함께 제공해야 하는 이유는 인터페이스를 구현하는 서로 다른 클래스 안에 동일한 기능을 반복해서 구현하고 싶지 않기 때문이다.

뉴욕 증권 거래소로부터 환율을 조회하는 일은 Exchange 인터페이스를 구현하는 NYSE 클래스에 특화된 기능이다.

하지만 환율이 제공되지 않았을 떄 'USD' 통화를 적용하는 것은 공유 가능한 공통 기능이다.

다음은 NYSE 클래스를 중첩 클래스 Exchange.Smart와 함께 사용하는 방법이다.

```java
float rate = new Exchange.Smart(new NYSE()).toUsd("EUR");
```

NYSE 뿐 아니라 모든 Exchange 구현체에 동시에 더 많은 기능을 추가해야 한다고 가정해보자.

USD를 EUR로 빈번하게 변환해야 하고 코드 중복도 피하고 싶다.

환율 변환이 필요한 모든 위치에서  EUR이라는 문자열 리터럴을 반복하고 싶지 않다.

우리에게 필요한 것은 USD를 EUR로 변환해주는 eurToUsd() 메서드이다.

이 메서드는 Exchange 인터페이스보다는 스마트 클래스에 추가하는 것이 낫다.

```java
interface Exchange {
  float rate(String source, String target);
  final class Smart {
    private final Exchange origin;
    public float toUsd(String source) {
      return this.origin.rate(source, "USD");
    }
    public float eurToUsd() {
      return this.toUsd("EUR");
    }
  }
}
```

이제부터는 단 한번의 메서드 호출만으로 EUR에서 USD로의 환율을 얻을 수 있다.

```java
float rate = new Exchange.Smart(new NYSE()).eurToUsd();
```

스마트 클래스의 크기는 점점 더 커지겠지만, Exchange 인터페이스는 작고, 높은 응집도를 유지할 수 있다.

---

