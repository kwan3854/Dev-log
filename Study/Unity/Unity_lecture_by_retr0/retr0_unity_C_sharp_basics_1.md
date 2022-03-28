# C# 프로그래밍 기초

## 변수와 함수의 이해

### 변수

**변수 (Variable)** : 값 (value) 이 할당되는 이름

```c#
int gold = 10000;
gold = gold - 200;
```

```c#
float itemWeight = 1.3f;
bool itemUsed = true;
string itemName = "Potion";
```

변수에 할당된 데이터는 런타임(게임 도중)에 얼마든지 접근하여 수정 가능

```c#
type name; // 선언
type name = value; // 선언과 동시에 값 초기화
```

---

### 함수

**함수 (Function)** : 미리 정해진 동작을 수행하는 코드 묶음

여러번 중복되는 코드를 간결하게 묶는다. 사용하고 싶을때 마다 불러서 쓴다.

```c#
void Attack(Monster target, int damage, int point)
{
  PlayAnimation();
  PlaySound();
  target.hp = target.hp - damage;
  exp = exp + point;
}

Monster ork
Attack(ork, 5, 30); // 코드 재사용

Monster goblin
Attack(goblin, 20. 5); // 코드 재사용
```

```c#
int GetRandomNumber() // 함수명 앞에 리턴값의 타입 지정
{
  int number = 0;
  number = 랜덤한 숫자;
  return number; // 함수의 결과 값 전달
}

int newNumber = GetRandomNumber(); // 함수의 결과 값 받아옴
```

---

- 함수 (혹은 메서드):
  - 어떤 곳에 미리 코드를 만들어 두어서, 여러곳에서 필요할때 마다 쓸 수 있는 코드 묶음
  - 입력을 받아 처리할 수도 있다
  - 결과를 주지 않거나(void), 결과로 어떤 값을 줄 수 있다

```c#
type FunctionName(type inputName)
{
  
}
```

