# Heap Code

Heap 영억에 메모리를 할당해서 사용하는 방법은 크게 세 가지가 있다.

-  C style
- C++ style
- Safer C++ style

위 세 가지 방법으로 작성된 코드를 살펴보고,

Safer C++ style 로 코드를 작성해야 하는 이유를 살펴보자.

## 1. C style

- malloc
- free

```c++
#include <stdlib.h>
#include <iostream>

class Cat
{
public:
  Cat()
  {
    std::cout << "meow" << std::endl;
  }
  ~Cat()
  {
    std::cout << "bye" << std::endl;
	}
};
int main()
{
  // C style heap Cat
  Cat* catp = (Cat*)malloc(sizeof(Cat)); // consturctor 호출x
  free(catp); // destructor 호출x
  
  // C style heap Cats(cat array)
  Cat* catap = (Cat*)malloc(sizeof(Cat)*5); // consturctor 호출x
  free(catap); // destructor 호출x
  
  // C style heap int
  int* ip = (int*)malloc(sizeof(int));
  *ip = 100;
  free(ip);
  
  // C style heap array
  int* ip = (int*)malloc(sizeof(int)*5);
  iap[0] = 100;
  free(iap);
}
```

C style 로 객체를 메모리 할당, 해제를 했더니,

생성자, 소멸자가 호출되지 않는다.

C++ 에서는 C style 의 메모리 할당과 해제는 사용하지 말자.

## 2. C++ style

- new
- delete

```c++
#include <stdlib.h>
#include <iostream>

class Cat
{
public:
  Cat()
  {
    std::cout << "meow" << std::endl;
  }
  ~Cat()
  {
    std::cout << "bye" << std::endl;
	}
};
int main()
{
  // C++ style heap Cat
  Cat* catp = new Cat; // consturctor 호출o
  delete catp; // destructor 호출o
  
  // C++ style heap Cats(cat array)
  Cat* catap = new Cat[5]; // consturctor 호출o (5번 호출)
  delete[] catap; // destructor 호출o (5번 호출)
  
  // C++ style heap int
  int* ip = new int;
  *ip = 100;
  delete ip;
  
  // C++ style heap array
  int* ip = new int[5];
  iap[0] = 100;
  delete iap;
}
```

이번에는 생성자, 소멸자 문제는 해결했다.

그러나, 이 방법 역시 문제가 있다.

만약, delete 를 깜빡하고 하지 않는다면 메모리 leak 이 발생할 수 있다.

## 3. Safer C++ style

### 객체의 경우

smart_ptr 를 사용하자.

```c++
class Cat
{
public:
  Cat()
  {
    std::cout << "meow" << std::endl;
  }
  ~Cat()
  {
    std::cout << "bye" << std::endl;
	}
};

int main()
{
  std::unique_ptr<Cat> catp = std::make_unique<Cat>();
}
```

```shell
prompt: g++ Heap1.cpp
prompt: ./a.out
meow
bye
```

따로 delete 를 하지 않아도 자동으로 소멸자가 호출된다.

### Array 의 경우

STL 의 Vector 를 사용한다.

```c++
class Cat
{
public:
  Cat()
  {
    std::cout << "meow" << std::endl;
  }
  ~Cat()
  {
    std::cout << "bye" << std::endl;
	}
};

int main()
{
  std::vector<Cat> cats(5);
}
```

```shell
prompt: g++ Heap1.cpp
prompt: ./a.out
meow
meow
meow
meow
meow
bye
bye
bye
bye
bye
```

생성자와 소멸자가 각각 5번 호출된다.

### fundamental (built-in) type 의 경우에도

```c++
class Cat
{
public:
  Cat()
  {
    std::cout << "meow" << std::endl;
  }
  ~Cat()
  {
    std::cout << "bye" << std::endl;
	}
};

int main()
{
  std::unique_ptr<int> ip = std::make_unique<int>();
  *ip = 100;
  
  std::vector<int> ints(5);
  ints[0] = 100;
}
```

## 4. Dynamic allocation (Heap allocation) 의 필요성

```c++
class Cat
{
public:
  Cat()
  {
    std::cout << "meow" << std::endl;
  }
  ~Cat()
  {
    std::cout << "bye" << std::endl;
	}
};

int main()
{
  std::cout << "How many cats do u need?" << std::endl;
  int catCount;
  std::cin >> catCount;
  
  std::vector<Cat> cats(catCount);
  
  return 0;
}
```

Dynamic allocation 을 통해서 다음과 같이 run-time 에 할당이 필요한 메모리 크기를 결정해서 사용할 수 있다.

