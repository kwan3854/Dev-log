# 디자인 패턴

## 1. 디자인 패턴이란?

### 디자인 패턴 (Design Pattern)

- 공통적으로 발생하는 문제에 대해 재사용 가능한 해결책 (템플릿)
- 아키텍처 설계 수준보다 낮은 수준의 설계

### 디자인 패턴의 혜택

- 쉽게 재사용 가능
- 설계 작업이 쉬워짐
- 디자인을 논의하기 위한 의사소통이 쉬워짐
- 객체지향 설계 원리를 잘 따르게 됨

## 2. 디자인 패턴의 종류

### 싱글톤 패턴 (Singleton Pattern)

- 객체를 강제적으로 하나만 생성할 수 있게 함

- 동일한 객체를 여러 개 생성할 필요 없는 경우

  예) DB 커넥션을 위한 인터페이스

- 방법

  1. 클래스 자체를 정적 변수로
  2. 생성자는 private로 선언
  3. 유일한 객체를 접근하는 정적 메서드

```java
public class SingletonClass {
  private static final SingletonClass ourInstance = new SingletonClass();
  
  public static SingletonClass getInstance() {
    return ourInstance;
  }
  
  private SingletonClass() {
    // constructor
  }
}
```

### 반복자 패턴 (Iterator Pattern)

- 집합 클래스(container class)의 자료구조와 상관없이 집합에 소속된 요소들을 쉽게 접근하기 쉽게 함
- 예) C++ STL의 iterator

*iterator 를 사용하지 않은 경우*

```c++
int vectorSum1(const vector<int>& V) {
  int sum = 0;
  for (int i = 0; i < V.size(); i++) {
    sum += V[i];
  }
  return sum;
}
```

*iterator 를 사용한 경우*

```c++
int vectorSum2(vector<int> V) {
  typedef vector<int>::iterator Iterator;
  int sum = 0;
  for (Iterator p = V.begin(); p != V.end(); ++p) {
    sum += *p;
  }
  return sum;
}
```

### 어댑터 패턴 (Adapter Pattern)

- 서비스의 인터페이스를 클라이언트의 인터페이스에 맞게 조정

- 호환되지 않는 서비스의 인터페이스를 연결 가능하게 함

- 컴포넌트를 수정하지 않아도 동작

- 의문

  어느 한쪽이 다른 한쪽에 맞게 바꿔주면 안되나?

  -> 양쪽 다 엮인 다른 코드들이 많아 못 바꿀 경우가 있다.

```java
import java.util.*;

public class AdapterExample {
  public static void main(String[] args) {
    ArrayList container = newArrayList();
    container.add(new Integer(1));
    container.add(new Integer(2));
    Iterator iterator = container.iterator();
    IterationAdapter iterationAdapter = new IterationAdapter(iterator);
    client(iterationAdapter);
  }
  
  public static void client(Enumeration e) {
    while (e.hasMoreElements()) {
      System.out.println(e.nextElement());
    }
  }
}


class IterationAdapter implements Enumeration {
  private Iterator adaptee;
  
  public IterationAdapter(Iterator iterator) {
    adaptee = iterator;
  }
  
  public Boolean hasMoreElements() {
    return adaptee.hasNext();
  }
  
  public Object nextElement() {
    return adaptee.next();
  }
}
```

### 데코레이터 패턴 (Decorator Pattern)

- 기존 클래스의 동작을 가볍고 유연하게 확장 (기능 추가)

- 기존 방법1: 수정에 의한 추가

  -> Open-Closed 원리에 위배

- 기존 방법2: 상속에 의한 추가

  - 기능의 조합 수 만큼의 서브클래스가 필요
  - 동적 확장 불가능

- 구성요소

  - 장식 대상 component 클래스와 확장 기능이 담긴 decorator

- decorator 객체가 재귀적 합성으로 component 객체를 wrapping

*사용 방법*

```java
public class Main {
  public static void main(String[] args) {
    // create a decorated Window with horizontal and vertical scrollbars
    Window decoratedWindow = new HorizontalScrollBarDecorator (
            new VerticalScrollBarDecorator(new SimpleWindow()));
    // print the Window's description
    System.out.println(decoratedWindow.getDescription());
  }
}
```

*decorator1*

```java
class VerticalScrollBarDecorator extends WindowDecorator {
  public VerticalScrollBarDecorator(Window decoratedWindow) {
    super(decoratedWindow);
  }
  
  public void draw() {
    drawVerticalScrollBar();
    decoratedWindow.draw();
  }
  
  private void drawVerticalScrollBar() {
    // draw the vertical scrollbar
  }
  
  public String getDescription() {
    return decoratedWindow.getDescription() + ", including vertical scrollbars";
  }
}
```

*decorator2*

```java
class HorizontalScrollBarDecorator extends WindowDecorator {
  public HorizontalScrollBarDecorator(Window decoratedWindow) {
    super(decoratedWindow);
  }
  
  public void draw() {
    drawHorizontalScrollBar();
    decoratedWindow.draw();
  }
  
  private void drawHorizontalScrollBar() {
    // draw the horizontal scrollbar
  }
  
  public String getDescription() {
    return decoratedWindow.getDescription() + ", including horizontal scrollbars";
  }
}
```

*사용 인터페이스*

```java
interface Window {
  public void draw(); // 윈도우 그림
  public String getDescription(); // 설명 출력
}
```

*component1*

```java
class SimpleWindow implements Window {
  public void draw() {
    System.out.println("Draw");
  }
  
  public String getDescription() {
    
  }
}
```

*decorator*

```java
abstract class WindowDecorator implements Window {
  protected Window decoratedWindow;
  
  public WindowDecorator(Window decoratedWindow) {
    this.decoratedWindow = decoratedWindow;
  }
}
```

### 팩토리 메소드 패턴 (Factory Method Pattern)

- 클라이언트에서 사용할 클래스의 객체를 생성하는 책임을 분리

  -> 객체 생성 변화에 대비

- 생성되는 객체의 종류를 신경 쓰고 싶지 않을 때 사용

  예) 문서편집기에서 document instance 생성 (html, pdf, word)

- 객체를 생성하기 위한 팩토리 메소드(createProduct)를 포함하는 추상 클래스(AbstractCreator)를 정의

```java
public Document CreateDocument(String type) {
  if (type.isEqual("html"))
    return new HtmlDocument();
  if (type.isEqual("proprietary"))
    return new MyDocument();
  if (type.isEqual("pdf"))
    return enw PdfDocument();
}

public void NewDocument(String type) {
  Document doc = CreateDocument(type);
  Docs.add(doc);
  Doc.open();
}
```

### 추상 팩토리 패턴 (Abstract Factory Pattern)

- 객체를 사용할 클라이언트에서 구체적인 객체 생성을 지정하는 책임을 분리하기 위한 것
- 팩토리 메소드와의 차이: 객체들의 패밀리를 생성할 수 있음

### 상태 패턴 (State Pattern)

- 상태에 따라 객체의 동작을 변경해야 하는 경우
- 맥락과 상태를 별도로 구현하여 융통성을 달성하기 위한 체계적이고 느슨한 결합 방식
- cf) 상태 변수의 if-else 조건 블럭

*상태 변수를 사용한 경우* (상태가 많을 경우 조건문이 너무 커질 수 있음)

```java
class Document is
  field state: string
  // ...
  method publish() is
    switch (state)
      "draft":
        state = "moderation"
        break
      "moderation":
        if (currentUser.role == 'admin')
          state = "published"
        break
      "published":
         break
  // ...
```

*상태 패턴을 사용한 경우*

*State interface*

```java
interface State {
  void writeName(StateContext context);
}
```

*State 클래스들*

```java
class State1 implements State {
  public void writeName(StateContext context) {
    ... // 할일
    context.setState(new State2());
  }
}

class State2 implements State {
  public void writeName(StateContext context) {
    ... // 할일
    context.setState(new State1());
  }
}
```

*context 클래스*

```java
class StateContext {
  private State state;
  
  public StateContext() {
    state = new State1();
  }
  
  void setState(State newState) {
    state = newState;
  }
  
  public void writeName(String name) {
    state.writeName(this);
  }
}
```

*context 사용*

```java
public static void main(String[] args) {
  StateContext context = enw StateContext();
  
  context.wrtieName("...");
  context.wrtieName("...");
  context.wrtieName("...");
  ...
}
```

### 옵저버 패턴 (Observer Pattern)

- Subject 의 데이터를 여러 observer 가 이용
- 데이터의 변경을 observer가 계속 검사하지 않고 subject 가 통지
- Subject 클래스: 옵저버 목록을 유지, 변경을 고지
- Observer 클래스: 변경을 통지 받고 접근을 요청

---

