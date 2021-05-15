# Operator Overloading, Friends, and References

## 1. 연산자 오버로딩

- **멤버 함수로서 오버로딩**

  연산의 왼쪽 값이 자신이고 오른쪽 값이 매개변수

  오버로딩의 종류에 따라 반드시 멤버 함수로 오버로딩 되어야 하는 것도 있음

- **friend 함수로서 오버로딩**

  연산의 왼쪽 값이 첫번째 매개변수이고 오른쪽 값이 두번째 매개변수

  이 방법이 편리할 때도 있지만, OOP적인 관점에서 올바른가에 대해서는 고민해볼 필요가 있음.

- **독립적으로 존재하는 오버로딩** (standalone)

  연산의 왼쪽 값이 첫번째 매개변수이고 오른쪽 값이 두번째 매개변수

## 2. 간단한 예시

### 2.1 독립적으로 존재하는(standalone) 오버로딩

해당 함수 내부에서는 private 객체에는 접근할 수 없기 때문에 getter 와 setter를 이용해야 할 것이다.

```c++
const Money operator+(const Money& amount1, const Money& amount2);
```

### 왜 const 를 리턴하는가?

만약 const 가 아닌 것을 리턴하게 되면 사용자가 이런짓도 할 수 있다.

```c++
(m1+m2).input() // const 리턴을 하지 않으면 이 문장이 문법적으로 이상이 없게 된다
```

문법적으로는 이상이 없는 표현이지만, anonymous object를 변경하려는 것이다.

다른 사람, 그리고 미래의 자신을 절대 믿으면서 코딩하지 말자.

### 2.2 멤버 함수로서 오버로딩

```c++
class Money
{
public:
  ...
  const Money operator+(const Money& amount2) const; // 마지막에 붙은 const의 의미는?
  
private:
  ...
};

const Money Money::operator+(const Money& secondOperand) const // 마지막에 붙은 const의 의미는?
{
  ... // 덧셈 작업
  return Money(finalDollars, finalCents);
}
```

함수 선언 뒤의 const 키워드는 함수가 클래스 멤버인 경우에만 붙일 수 있다.

해당 함수가 속한 객체의 멤버를 변경할 수 없다는 의미이다.

## 3. 어떤 방식으로 연산자 오버로딩 하는게 좋은가?

OOP 관점에서는 멤버 방식의 연산자 오버로딩하는 것이 좋다.

- 캡슐화라는 관점에서 OOP 의 정신(?)을 유지할 수 있다.

- 더 효율적이다.

  getter 와 setter를 쓸 필요가 없다.

- 그러나 **큰 단점** 이 하나 있다. (4에서 설명)

## 4. 생성자와 자동 형변환

다음처럼 멤버 연산자 오버로딩이 되어있다고 해 보자.

```c++
const Money operator+(const Money& amount);
```

그리고 Money 클래스에는 다음과 같은 생성자가 있다고 한다.

```c++
class Money
{
public:
  Money();
  Money(double amount);
  Money(int theDollars, int theCents);
  Money(int theDollars); // 여기에 주목!
  ...
};
```

이렇다고 할때 다음 코드는 어떻게 해석될까?

```c++
fullAmount = baseAmount + 25; // Money 타입과 int 타입의 +연산은 오버로딩 한적이 없다.
```

1. Exact match 를 가장 먼저 찾는다: operator+(Money&, int)
2. 없다. 호환되는 Match를 찾는다. (예를들어 int -> double 처럼)
   1. operator+(Money&, Money&)
   2. int -> Money 를 시도한다.
   3. 적절한 생성자를 찾는다: Money(int)

그렇다면, 직관적으로 생각할때 이 경우도 당연히 되어야 할 것이다.

```c++
fullAmount = baseAmount + 25;
fullAmount2 = 25 + baseAmount; // 이 경우도 당연히 될까?
```

**안된다!**

멤버 연산자 오버로딩에서는 왼쪽 피연산자가 Money 일 때 그쪽 객체에서 연산자 오버로딩을 호출하는 방식이다.

25의 연산자 오버로딩, 즉, int type에서는 Money 타입과의 +연산자 오버로딩이 되어있지 않다.

**이것이 바로 멤버 방식의 연산자 오버로딩의 큰 단점이다.**

아래와 같이 standalone 방식이나 friend 방식을 사용했으면 문제없이 둘 다 호환이 된다.

```c++
const Money operator+(const Money& amount1, const Money& amount2);
```

## 5. Friend 함수란?

private, protected, public 접근 제한자가 있다.

자식에게는 나의 개인적인(private) 것 말고는 모두 보여줄 수 있었다.

그렇다면 나의 절친(friend)에게는?

나의 **개인적인(private) 것 까지 모두 공유할 수 있다.** ( ~~흠... 진짜?~~ )

단, 친구는 내가 친구라고 생각해야 친구지, 저 사람이 나를 친구라고 생각한다고 내 친구가 되는것은 아니다.

즉, **friend keyword 는 클래스 정의부 안에 선언된다.** (그렇다고 **친구(friend)가 우리 가족(member)는 아니다.** )

### friend 함수를 이용해 연산자 오버로딩을 하면

- 연산자 오버로딩이 friend 함수를 이용하는 가장 흔한 방법이다.

- 효율성이 좋아진다

  getter, setter 사용을 피할 수 있다.

```c++
class Money
{
public:
  Money();
  Money(double amount);
  Money(int theDollars, int theCents);
  Money(int theDollars);
  ...
  friend const Money operator+(const Money& amount1, const Money& amount2); // 이 부분
  // 멤버가 아니기 때문에 매개변수가 2개이다.
  ...
};
```

```c++
// 정의 부분은 일반 standalone 함수와 같다.
const Money operator+(const Money& amount1, const Money& amount2)
{
  ...
  return Money(finalDollars, finalCents);
}
```

friend 함수의 사용은 OOP 측면에서 비판이 있을 수 있지만,

연산자 오버로딩할 때 큰 이점이 있다.

## 6. reference 의 사용 (cin 과 cout 오버로딩)

- 겉보기에 위험해 보인다.

- 몇가지 case 에서 매우 유용하다.

  1. Call-by-reference

     보통 이 경우에 많이 쓰인다.

  2. Returning a reference

     - 연산자 오버로딩을 더욱 자연스럽게 기능하게 만들 수 있다.
     - 별명을 리턴한다고 생각하면 된다.

```c++
double& sampleFunction(double& variable);
```

이 경우 절대 리턴의 형태가 `return x+5;` 같은 형태면 안된다.

참조할 메모리 공간이 없기 때문. 리턴값은 **반드시 참조할 메모리가 있어야 한다.**

```c++
double& sampleFunction(double& variable)
{
  // 뭔가 variable을 가공한다.
  return variable; // 가공된 variable 리턴
}
```

그래서 보통 위와 같은 형태로 사용되고, 이런 형태는 특정한 연산자 오버로딩시에 유용하다.

```c++
class Money
{
public:
  Money();
  Money(double amount);
  Money(int theDollars, int theCents);
  Money(int theDollars);
  ...
  friend const Money operator+(const Money& amount1, const Money& amount2);
  friend ostream& operator<<(ostream& outputStream, const Money& amount); // cout
  friend istream& operator>>(istream& inputStream, Money& amount); // cin
  ...
};
```

참조를 리턴함으로서, 다음과 같은 cascade 한 사용이 가능해진다.

```c++
cout << "My money is " << myMoney << "your Money is " << yourMoney << endl;
```

## 7. 할당 연산자 '='

- **반드시 멤버로** 연산자 오버로딩 되어야 한다.

- 자동으로 정의되어 있다

  - default 할당 연산자:
    - 멤버별로 복사
    - 멤버별로 단순히 값을 복사하기 때문에 문제가 발생할 수 있다.

- 간단한 형태의 클래스라면 기본 할당 연산자가 괜찮을 수 있다.

  그러나 포인터가 멤버에 포함되어 있다면? -> 반드시 할당 연산자를 수동으로 만들어 줘야 한다.

  객체가 소멸되면서 해당 주소는 쓰레기 값을 가리킬 것이기 때문.

## 8. 증감 연산자 (++, --)

- 각각 두가지 버전이 있다.

  - prefix
  - postfix

- 오버로딩시 반드시 구분해야 한다.

  - 일반적인 오버로딩 방법 사용시 -> prefix

  - 오버로딩 시 두번째 파라미터를 int타입으로 추가시 -> postfix

    두번째 파라미터인 int는 특별한 의미가 있는게 아니라, 단순히 컴파일러가 인식하게 표시하는 용도

## 9. Array 연산자 [ ]

- 내가 만든 class의 객체에 사용할 수 있다.
- 연산자는 반드시 reference를 리턴해야 한다.
- 반드시 멤버 함수여야 한다.

## 10. Function call 연산자 ( )

- 반드시 멤버 함수로 오버로딩되어야 한다.
- 모든 가능한 인자 개수의 경우를 오버로딩해야 한다.

참고

```c++
// 생성자와 function call 연산자 비교
List l;
List(30); // 생성자 호출
l(30); // function call 연산자
```

## 11. 기타 연산자

- &&, ||, 콤마 연산자

  - 기본적으로 bool type으로 동작하게 미리 정의되어있다.

  - 오버로딩하면 short-circuit evaluation 이 동작하지 않는다.

    예를 들어 '||' 일때, 앞의 것이 false 이면 뒤에 것이 뭐든간에 상관없이 false 이니까 뒤는 보지도 않는다.

    이 방식이 오버로딩되면 동작하지 않는다.

- 일반적으로 이러한 연산자는 오버로딩 하지 않는다.