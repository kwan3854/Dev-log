# Constructor (생성자)

> 우리에게 장비를 주시오. 그러면 그 일을 해낼 것이오
>
> -윈스턴 처칠, 라디오 방송 (1941. 2. 9.)

클래스를 사용하여 프로그래밍할 때 이용되는 여러 가지 중요한 툴을 소개한다.

그 중 가장 중요한 툴은 클래스 객체를 초기화하는 데 사용되는 일종의 함수인 클래스 생성자이다.

## 1. 생성자

> 시작을 잘하면 반은 성공한 것이다.

클래스를 정의할 때 **생성자(constructor)** 로 알려진 특별한 종류의 멤버 함수를 정의할 수 있다.

생성자는 이 클래스의 객체가 선언될 때 자동으로 호출되는 멤버 함수이다.

생성자는 멤버 변수의 값을 초기화하는 데 뿐만 아니라 다른 종류의 초기화에도 사용된다.

## 2. 생성자 정의

1. 클래스와 똑같은 이름을 가져야 한다.

2. 어떤 값도 리턴할 수 없다. (void 형도 아님)

3. 일반적으로 생성자는 public 멤버 함수로 만들어야 한다.

   모든 생성자를 private 멤버가 되게 하면 이 클래스형의 객체를 선언할 수 없게 된다.

   (싱글톤 패턴이 예외 케이스)

```c++
class DayOfYear
{
public:
  DayOfYear(int monthValue, int dayValue);
  //month와 day를 인자 값으로 초기화한다.
  
  void input();
  void output();
  void set(int newMonth, int newDay);
  void set(int newMonth);
  int getMonthNumber();
  int getDay();
private:
  int month;
  int day;
};
```

## 3. 생성자 호출

```c++
DayOfYear date1(7, 4), date2(5, 5);
```

- 생성자가 호출됨
- 괄호안의 값들이 생성자에게 전달됨

### 함정: 인자가 없는 생성자

인자가 없는 생성자를 호출하길 원하면 괄호를 사용하지 않는다.

```c++
DayOfYear date1(2, 21), date(5), date3;
```

**왜 일까?**

```c++
DayOfYear date1; // 맞는 방법

// 만약 아래의 방법이 인정된다면?
DayOfYear date(); // 컴파일러는 이것이 함수의 declaration 혹은 prototype 인지, 생성자인지 헷갈리게 된다.

date1 = DayOfYear(); // 이것은 맞는 방법이다.
// 명시적인 호출에서는 괄호는 넣는것이 맞다.
```

### Constructor Equivalency

생성자를 다른 멤버 함수처럼 호출할 수 없다.

```c++
DayOfYear date1, date2;
date1.DayOfYear(7, 4); // ILLEGAL!
date2.DayOfYear(5, 5); // ILLEGAL!
```

## 4. 생성자 코드

```c++
DayOfYear::DayOfYear(int monthValue, int dayValue)
{
  month = monthValue;
  day = dayValue;
}
```

다음과 같이 작성할 수도 있다.

```c++
DayOfYear::DayOfYear(int monthValue, int dayValue) : month(monthValue), day(dayValue)
{
  // 의도적인 공백
}
```

보통 이 형태의 생성자 코드가 더 선호된다.

- Body 부분이 꼭 공백이어야 할 필요는 없다.
- 데이터를 validate 하는것이 중요하다.
  - 적절한 데이터가 Private 멤버 변수들에게 할당되는지 확실히 해야한다.
  - OOP 의 강력한 원칙이다.

## 5. Overloaded Constructors

- 생성자는 여타 다른 함수들처럼 overload 될 수 있다.
- signature 가 같아야 한다.
  - 함수의 이름
  - 매개변수의 list
- 모든 가능한 매개변수의 list를 제공해야 한다.
  - 특히 매개변수의 개수 측면에서

```c++
class DayOfYear
{
public:
  DayOfYear(int monthValue, int dayValue);
  DayOfYear(int monthValue);
  DayOfYear(); // default constructor

  ...
```

기본적으로는 위 처럼 정의한다. (책에서의 방법)

OOP적인 측면에서 한 가지 팁이 있다면, (개인적으로 생각하는 좋은 방법)

가장 보편적인 생성자 하나를 메인 생성자로 하고, 메인 생성자 하나에만 구체적인 구현을 적용하고,

나머지 생성자는 직접 구현 없이 메인 생성자를 호출하는 형식으로 생성자를 만들면 좋다.

**즉 모든 구체적인 생성자의 구현은 메인 생성자 하나에만 넣는다.**

```c++
class DayOfYear
{
public:
  DayOfYear(); // 부생성자
  DayOfYear(int monthValue); // 부생성자
  DayOfYear(int monthValue, int dayValue); // 메인 생성자

  ...
};

DayOfYear::DayOfYear() : DayOfYear(0, 0)
{
  // 의도적인 공백
}
 
DayOfYear::DayOfYear(int monthValue) : DayOfYear(monthValue, 0)
{
  // 의도적인 공백
}

DayOfYear::DayOfYear(int monthValue, int dayValue) : month(monthValue), day(dayValue)
{
  // 의도적인 공백
}
```

직접 구현이 적용된 메인 생성자를 항상 가장 아래에 배치하는 것을 추천한다. (가독성 측면에서)

## 6. 명시적인 생성자 호출

```c++
DayOfYear holiday(7, 4); // 객체의 declaration 에서 생성자 호출
holiday = DayOfYear(5, 5); // 명시적인 생성자 호출
```

명시적 생성자 호출에서는...

1. 새로운 anonymous object 를 반환하게 된다
2. 그것이 현재의 오브젝트에 할당된다.

## 7. 디폴트 생성자

디폴트 생성자는 인자를 취하지 않는 생성자이다.

클래스를 정의할 때 어떤 종류의 생성자도 포함시키지 않으면 디폴트 생성자가 자동으로 생성된다.

**주의:** 생성자를 하나라도 정의하면 디폴트 생성자는 제공되지 않는다.

**따라서 항상 디폴트 생성자를 포함시키는게 좋다.**

