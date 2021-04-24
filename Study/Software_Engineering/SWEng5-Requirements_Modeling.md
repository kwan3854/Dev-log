# Requirements Modeling

## 1. 요구 모델링 (Requirements Modeling)

- 분석한 요구사항들을 개발자가 이해하는 솔루션 관점으로 표현

- 고객과 개발자가 무엇이 개발되고 있는지에 동의하는 것을 목적으로하는 요구 명세를 생성

  (cf. 요구 분석: 시스템에 대한 형식적 설명 제공)

- 고객과 개발자가 동일한 방식으로 이해할 수 있어야 함

---

## 2. 모델링 기초

- 복잡한 시스템을 다루는 방법
  - 전체를 다루기에는 너무 복잡한 대상을 추상화 또는 단순화
- 코드를 명확히 이해하기 위해 필요
- 시스템 내부 동작을 문서화, 전달 및 시각화

### 모델링을 하는 이유

1. 복잡함을 잘 관리하기 위하여
2. 형체가 없는 소프트웨어의 구조를 시각화 하기 위하여
3. 다른 사람과 커뮤니케이션 하기 위하여
4. 문제 도메인 및 제품 요구 사항을 이해하기 위하여
5. 개발 중인 시스템을 이해하기 위하여
6. 구현하기 전에 잠재적 솔루션을 실험해 보기 위하여
7. 기존 시스템의 문서화

### 관점(Perspective)과 추상화 수준(Abstraction Level)

모델을 만드는 목적에 따라 달라짐

### 소프트웨어와 모델링

- 그래픽 기호와 주석으로 구성된 시각적 다이어그램
- 모델링 목적에 따라 형식 결정
- 관점
  - 비지니스 프로세스
  - 구조
  - 동작

### 모델 사이의 관계

- 모델링 결과는 다른 모델링 작업에 영향을 줄 수 있음
  - 예: 도메인 모델의 용어와 개념 -> 정적 모델링의 클래스
  - 예: Tetris class 안에서 Screen Class 와 Block Class 를 정의한다면?

---

## 3. UML (Unified Modeling Language)

- 가장 보편적인 소프트웨어 모델 표기법
- 객체지향 소프트웨어를 모델링
  - 시스템의 여러 측면을 그림으로 모델링
  - 하드웨어의 회로도 같은 의미
- 1990년대 후반, 다음과 같은 표기법을 결합하여 탄생
  - OMT (Object Modeling Technique) [Rumbaugh, 1991]
  - Booch [Booch, 1994]
  - OOSE (Object-Oriented Software Engineering)

### UML 다이어그램

- 구조 다이어그램, 동작 다이어그램으로 구성
- 정적 관점
  - Structure Diagram - 시간 구성 요소가 없음
  - 예: 클래스 및 함수 이름, 로컬 및 글로벌 데이터 구조 등
- 동적 관점
  - Behavior Diagram - 시간의 흐름에 따른 변화를 보여줌
  - 예: 제어 흐름, 데이터의 변화 등

---

## 4. 정적 모델링 (Static Modeling)

- 객체들의 공통 구조와 동작들을 추상화
- 객체지향 기본 개념의 이해가 필요
  - 객체와 속성
  - 캡슐화(encapsulation): 속성들과 오퍼레이션들을 모아서 단위화
    - 가시성(visibility): public, protected, private (-> 정보은닉)
  - 연관(association): 서비스를 제공하는 객체(server)와 서비스를 요청하는 객체(client)가 상호작용하는 관계
    - 속성 및 오퍼레이션들의 가시성과도 연결됨
  - 상속(inheritance): 상위 클래스가 갖는 속성과 연산을 하위 개념의 클래스가 물려받음
  - 다형성(polymorphism): 객체가 여러가지 형태를 가짐. 같은 이름의 동작을 다른 객체 또는 서브 클래스에 호출할 수 있는 특징
- 클래스 다이어그램이 대표적
  - 클래스 및 클래스 사이의 관계를 표현
  - 도메인 개념과 속성 표현

### 클래스 다이어그램 (Class Diagram)

- UML에서 가장 대표적인 다이어그램
- 클래스
  - 세 개의 부분으로 나뉨 (이름, 속성, 오퍼레이션)
  - 추상 클래스는 *이탤릭체* , 인터페이스 클래스는 <<inerface>> 추가
- 클래스 혹은 객체 사이의 관계들을 표현함
  - 연관, 일반화, 의존, 실체화

```
// BankAccount
-balance:double=0.0
-transactionHistory:Transaction[0..*]{ordered}
-/hasFreeChecking:Boolean
```

`[가시성][변수명]:[타입][다수성]=[디폴트값]{속성}`

- 속성 형식
  - 가시성 (visibility)
    - 속성에 대한 접근성
    - public(+), private(-), protected(#)
  - 타입
    - 속성의 타입
    - 클래스, 인터페이스, 정수, 실수 등
  - 다수성 (multiplicity)
    - 자료가 참조하는 객체의 수
    - 배열 등
  - 속성
    - 추가 속성: ordered, unordered, unique, nonunique, readonly 등
  - 밑줄은 static attribute 을 의미함 (클래스 객체에 속하는 속성들)

```
+deposit(amount:double):void
+withdraw(in amount:double):void
+getBalance():double{query}
-logTransaction(Transaction)
-assertInvariant()
```

`[가시성][동작이름]([매개변수]):[리턴타입]{속성}`

[매개변수] 를 확대하면

`[방향][매개변수 이름]:[타입]=[디폴트값]`

- 오퍼레이션 형식
  - 매개변수 형식
    - 방향: in, out, inout
  - 속성
    - 추가 속성
    - query가 대표적: 값을 리턴하고 객체의 상태를 변경하지 않음
  - 밑줄은 static operation 을 의미함 (클래스 객체를 위한 오퍼레이션)

---

## 5. 동적 모델링

### 동적 뷰 (Dynamic View)

- 소프트웨어가 실행될 때 변경 될 수 있는 뷰

  ex) 객체 간 상호작용 패턴

### 상호작용 다이어그램

- 시스템 동작을 모델링

  -> "런타임에 객체가 어떻게 작동하여 결과를 내는가"

- 시스템 전체 동작, 오퍼레이션 모델링

### UML 상호작용 다이어그램

- 시퀸스 다이어그램: 객체 간에 교환하는 메시지의 상대적 순서 중점
- 협동 다이어그램: 모델 요소 간의 구조적 관계 중점
- 상태 다이어그램: 수신 이벤트와 응답에 따른 객체 상태 변환에 중점

### 시퀸스 다이어그램 (Sequence Diagram)

- 메시지 교환을 시각적 관계로 표시
- Use case 이벤트 흐름 표시
- 작성 과정
  - 참여하는 객체 파악
  - 파악한 객체를 x축에 나열, lifeline 작성
    - Use case 에 기술된 이벤트 순서에 따라 메시지 호출을 화살표로 표시

### 협동 다이어그램 (Collaboration Diagram)

- 인스턴스들이 어떻게 협동하는지 표시
- 기능은 Sequence diagram과 같음, 객체의 정적인 구조 중심
- 두 가지 조합
  - 상호작용에 필요한 객체들 간의 링크를 포함한 객체 다이어그램
  - 상호작용을 정의하는 객체 간의 메시지
- 메세지 번호로 순서를 표시

### 상태 다이어그램 (State Diagram)

- 수신 이벤트와 응답을 기반으로 객체의 상태 전환을 모델링
- 고려사항
  - 외부 이벤트의 영향
  - 상태 종속적인 객체 반응은 어떻게 변환하는가
  - 상태를 체크하기 위한 속성의 조건
- 사례: 도서관 시스템에서 "책"의 상태

---

## 6. 제어 모델링

### 액티비티 다이어그램 (Activity Diagram)

- 액티비티(알고리즘 or 프로세스) 사이의 제어 흐름 표시
- Use case 도 표현 가능 (use case diagram 보다 정형화)
- Swim Lane 으로 표시 가능 (병렬적으로 표시 가능)

---

## 7. 모델 검증

요구가 정확히 모델링 되었는지 확인

### 모델 검증 방법

- 리뷰: 사람이 눈으로 확인 (체크리스트)
- 테스팅: 모델에 근거하여 테스트 데이터를 찾고 구현 후 테스트
- 정형적 방법: 수학적으로 증명
- 프로토타이핑: 프로토타입을 제작하여 확인
- 요구 추적: 모델로부터 요구를 거꾸로 추적

### 일관성 체크 (Consistency Check)

- 오류가 없고 모델 사이에 일관성이 있는지 확인
  1. Use case diagram과 sequence diagram
     - Use case 에 대한 명세가 기술되어 있고 매칭되는 시퀸스 다이어그램이 있는지 확인
  2. Sequence diagram과 class diagram
     - Sequence diagram의 클래스와 메시지가 class diagram에 빠지지 않고 표현되었는지 체크
  3. State diagram과 class diagram
- 체크 리스트

---
