# 구현 (Implementation) (Part 1)

## 1. 구현

- 구현 로드맵
  - 소프트웨어 제품의 품질은 결국 소스코드 문제
- 구현 작업
  - 설계 명세서에 나타낸대로 요구를 만족할 수 있도록 프로그래밍
  - 상세 설계, 사용자 지침서에 기술된 것과 일치되도록 코딩
    - 전 단계의 문서(아키텍처 설계서, 요구 분석서 등)를 참조
- 구현 단계 목표
  - 오류가 적은 품질 좋은 프로그램
- 작업 과정
  1. 소스 코드의 스타일 통일을 위해 코딩 표준 설정
  2. 아키텍처설계 결과, 프레임워크 패키지와 응용 패키지를 결정
  3. 클래스 구현이 끝나는 대로 인스펙션
  4. 클래스 단위로 테스트
  5. 클래스나 패키지를 릴리스 하여 으용 시스템으로 통합

## 2. 자주 발생하는 오류

### 흔히 발견되는 오류는 정해져 있음

- 메모리 누수 (memory leak)
- 중복된 프리 선언
- NULL의 사용
- 별칭의 남용
- 배열 인덱스 오류
- 수식 예외 오류
- 하나 차이에 의한 오류
- 사용자 정의 자료형 오류
- 스트링 처리 오류
- 버퍼 오류
- 동기화 오류

### 메모리 누수

- 동적 할당된 메모리가 해제되지 않고 프로그램에 계속 남아 있는 현상
- 장기간 수행되는 시스템에서 치명적인 영향을 줌
- Garbage collection 이 없는 언어 (C, C++)

```c
void f()
{
  int *ptr = (int *) malloc(sizeof(int));
  
  /* Do some work */
  
  return; /* Return without freeing ptr */
}
```

### 중복된 momery 방출

- 이미 해제된 메모리를 다시 해제

```c
main()
{
  char *str;
  str = (char *) malloc(10);
  
  if (global == 0)
    free(str);
  
  free(str); // str is already freed
}
```

### NULL

- NULL 포인터가 가리키는 곳의 콘텐츠를 접근하려고 하면 오류

```c
char *ch = NULL;
ch = malloc(size);
*ch = 'c'; // ch may be NULL
```

### 별칭(Alias)의 남용

- 같은 공간을 공유하는 다른 이름
- 다른 공간인 것으로 잘못 알고 사용되면 오류 발생

```c
char src[256];
char *dst;
dst = src;

// ...

strcat(dst, src);
```

### 배열 인덱스 오류

- 배열 인덱스가 한도를 벗어난 경우
- 배열 인덱스가 음수 값을 갖는 경우

```c
dataArray[80];
for (i = 0; i <= 80; i++)
  dataArray[i] = 0;
```

### 수식 예외 오류

- 0으로 나눔
- 부동 소수점 예외

### 하나 차이에 의한 오류

- 0으로 시작하여야 할 것을 1로 시작
- <= N으로 써야 할 곳에 <N 을 쓴 경우
- 컴파일러나 테스트 도구에 의하여 검출되지 않는 경우가 많음

```c
typedef enum{A, B, C, D} grade;
void foo(grade X)
{
  int 1, m;
  l = GLOBAL_ARRAY[x-1]; // Underflow possible
  m = GLOBAL_ARRAY[x+1]; // Overflow possible
}
```

### 사용자 정의 자료형 오류

- 오버플로우나 언더플로우 오류가 쉽게 발생
- 사용자 정의 자료형의 값을 다룰 때는 특별히 주의

### 문자열 처리 오류

- strcpy, strcat, sprint 등
- 매개변수가 NULL
- 스트링이 NULL로 끝나지 않을 경우
- destination 공간이 충분히 크지 않을 경우

```c
char s1[] = "Hello";
char s2[] = "World";
strcat(s1, s2);
```

### 버퍼 오류

- 버퍼의 크기보다 더 큰 데이터 입력 -> Buffer overflow
- 해킹에 이용

```c
void mygets(char *str)
{
  int ch;
  while (ch = getchar() != '\n' && ch != '\0')
    *(str++) = ch;
  *str = '\0';
}

main()
{
  char s2[4];
  mygets(s2);
}
```

### 동기화 오류

- 멀티 쓰레드에서 공통 자원에 접근 시 발생
- 교착 상태 (deadlock)
  - 다수의 쓰레드가 서로 자원을 점유한 상태로 대기
- 경쟁 상황 (Race condition)
  - 여러 쓰레드에서 같은 자원에 동시에 접근할 경우 접근 타이밍에 따라 결과가 다를 수 있는 경우
- 모순이 있는 동기화
  - lock과 unlcok을 번갈아 하는 상황에서 오류가 많이 발생

## 3. 코딩 스타일

### 좋은 코딩 스타일

- 간결합: 복잡하지 않고 명확
- 가독성: 대충 훑어봐도 이해 가능 (문장 구조, 편집 상태 등)
- 시스템이 잘 설계되었는가에 좌우됨
  - 모듈화, 높은 응집력, 낮은 결합도를 달성하였다면 모듈은 간단해짐

### 명명 규칙

- 카멜 케이스 (camelCase)

  - 여러 단어를 붙여 쓰되 각 단어의 첫 글자는 대문자

  - 가장 첫 문자는 소문자 (lower camel case)

    예) thisIsAnExample

  - Java의 필드, 메소드 및 변수 이름

- 파스칼 케이스 (PascalCase or upper camel case)

  - 여러 단어를 함께 붙여 쓰되 각 단어의 첫 글자는 대문자

    예) ThisIsAnExample

  - Java의 클래스, 인터페이스

- 스네이크 케이스 (snake_case)

  - 각 단어를 _ 로 연결

    예) this_is_an_example

  - python, php, perl 등

- 상수는 모두 대문자로

  ```java
  public static final double SPEED_OF_LIGHT = 299792458; // in m/s
  ```

- 함수/메서드 이름

  - 소문자로 시작

    ```java
    public void setTitle(String t) { ... }
    ```

  - 멤버 변수/필드의 값을 리턴하는 함수(accessor)는 "get"

    ```java
    public String getTitle() {return title;}
    ```

  - 조건을 묻는 bool 함수 이름은 보통 "is"로 시작

    ```java
    public boolean isEquilateralTriangle(int b, int c, int d) { ... }
    ```

- 변수 이름

  - 일반적으로 소문자로 시작

  - 구체적인 이름 사용

  - 변수 이름의 길이

    - 너무 길어도 안됨

    ```java
    hypotenuseOfTriangle = 6 * (2 + hypotenuseOfTriangle) + 7
      / hypotenuseOfTriangle - hypotenuseOfTriangle;
    x = 6 * (2 + x) + 7 / x - x; // 같은 문장
    ```

    - 대상이 사용된 위치를 고려
      - 매개 변수: 되도록 짧게
      - 필드 변수: 가능한 구체적으로

- 헝가리안 표기법 (Charles Simonyi, Microsoft)

  - 변수 이름 앞에 타입 명시

    - lAccountNum : long integer(l) 타입의 변수
    - arru8NumberList: unsigned 8-비트 정수(arru8) 변수의 배열
    - bReadLine(bPort, &arru8NumberList): 바이트값 리턴
    - strName: 스트링을 가진 변수

  - 변수의 목적 또는 변수가 무엇인지에 대한 힌트 명시

    - rwPosition: 행(rw)을 나타내는 변수

    - szName: '\0'로 끝나는 스트링(sz) 변수

      sz -> null terminated string (String Zero)

### 코딩 형식

- 일관된 형식 사용

- 한 줄에 한 명령어만 입력 (예외 있음)

- 가로 세로 스크롤 없이 읽을 수 있게 (약 한 줄에 80자)

- 들여쓰기와 괄호

  - 프로그램 구조를 명확하게 하기 위해 사용
  - 문장의 일부와 선언문은 들여 쓰기 (일반적으로 4칸)

- GNU

  - 수평으로 많은 자리를 차지

  ```c
  if (x < y)
    {
      x = y;
    }
  ```

- K&R

  - 수평, 수직 모두 간결
  - 여는 괄호를 찾기 힘들 수 있음

  예) C, Java 계열, 구글, Javascript

  ```c
  if (x < y) {
    x = y;
  }
  ```

- BSD

  - 괄호의 소속을 분명히 함
  - 수직 방향으로 길어질 수 있음

  예) 비주얼 스튜디오

  ```c
  if (x < y)
  {
    x = y;
  }
  ```

- 블록은 항상 괄호 사용

  ```c
  if (flag) validate(); update(); // 잘못된 예시
  ```

  ```c
  if (flag) {
    alidate();
    update();
  }
  ```

- 키워드와 다음 괄호 사이에 공백 (함수 호출과 구분)

  ```c
  if (flag) {...} // O
  if(flag) {...} // X
  
  fuc(...); // 함수 호출은 붙여서
  ```

### 문장과 수식

- 여러 번 사용되는 문장이나 수식은 메서드나 클래스로 패키지화

- 블록 문장

  - 제어구조가 중첩되었을 때 생기는 혼란을 줄일 수 있음

    ```c
    if (x >= 0)
      if (x > 0) positiveX();
    else // Oops! Actually matches most recent if!
      negativeX();
    ```

- 수식

  - 괄호를 이용하여 오퍼레이션의 순서를 명확히 작성

    ```c
    // Extraneous but useful parentheses.
    int width = ((buffer * offset) / pixelWidth) + gap;
    ```

### 오류 처리

- 잘못된 데이터를 어떻게 다룰 것인가

  예) 실제로는 없는 계좌번호

- 매개변수 오류

  - 잘못된 매개변수를 배제

  - 함수 초기에 입력 유효성 검사

  - 특정 타입만 받는 함수 사용

    ```java
    // 매개변수로 "car", "truck", "bus"만을 받음
    evaluate(String vehicleP); // 적합하지 않음
    evaluate(SpecializedVehicle vehicleP); // 특별한 타입 선언
    ```

- 입력 오류 방지

  - 입력 폼 (체크박스, 리스트 등) 사용, 디폴트 값을 지정
  - 입력 유효성 검사

### 주석

- 프로그램 작성, 디버깅을 도움

- 다른 사람들이 프로그램을 이해하기 쉽도록 함

- 프로그램 작성 전에 주석부터 작성

- 주석 다는 법

  - 불필요한 단어는 생략
  - 과도한 주석 피함 (모든 줄에 주석)

- 클래스 불변 조건

  - 클래스의 개별 속성에 대한 의미와 제약 기술

  ```java
  /** The hour of the day, in 0..23. */
  private int hr;
  /** temps[0..numRecorded-1] are the recorded temperatures */
  private double[] temps;
  ```

- 메서드 주석

  - 메서드가 하는 일을 설명
    - 파라미터: @param
    - 리턴값: @return

  ```java
  /**
   * Returns the absolute value of an {@code int} value.
   * If the arguemnt is not negative, the argument is returned.
   * If the argument is negative, the negation of the argument is returned.
   *
   * @param a the argument whose absolute value is to be determined
   * @return the absolute value of the argument.
   */
       public static int abs(int a) {...}
  ```

- 클래스 주석

  - 클래스 앞 부분에 클래스의 용도를 설명하는 주석 작성
  - 설명, 저자, 마지막 수정일 등

  ```java
  /** An object of class Auto represents a car.
     @Author: ...
     Date of last modification: 25 November 2019 */
     public class Auto {...}
  ```

- 문장 주석

  - 논리적 단위(블럭)로 그룹화

  ```java
  // Truthify x >= y by swapping x and y if needed.
  if (x < y) {
    int tmp = x;
    x = y;
    y = tmp;
  }
  ```

  

