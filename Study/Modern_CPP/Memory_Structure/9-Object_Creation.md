# Object Creation

이전에 언급했듯, Java 에서의 습관 때문인지 간혹 object creation 시, 항상 new 를 통해 object 를 생성하는 개발자들이 있다.

그러나, modern c++ 에 들어와서는, pointer 와 new 를 통해 object 를 생성하는 일은 아예 없다고 봐도 과언이 아니다.

그 이유는, smartPtr 의 등장 때문이다.

### 코드

```c++
class Person
{
public:
  Person(std::string name, int age)
    : name(std::move(name)), age(age) {};
private:
  std::string name;
  int age;
}
```

```c++
int main()
{
  Person a("nocope", 31); // stack 에 object 가 생성됨.
  
  Person* ap = new Person(); // heap 에 object 가 생성되고 stack 에 생성된 pointer 이를 가리킴.
  delete ap;
}
```

new 를 통해 할당할 경우의 단점

1. memory leak 발생 가능성
2. allocation 이 느리다. (heap 은 stack 보다 느림)

### 결론

- stack 에 올릴 수 있는 상황이라면 stack 에 올리자.
- 반드시 heap 에 object 를 생성해야 하는 상황이 생기면, smart pointer 를 사용하자.

