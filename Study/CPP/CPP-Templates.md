# Templates

- Polymorphism (in compile-time)
- A single implementation for multiple data types
- Allow very "general" definitions for functions and classes

## 1. Function Template

```c++
// overloading
void swapValues(char& var1, char& var2)
{
  char temp;
  temp = var1;
  var1 = var2;
  var2 = temp;
}

// function template
template<typename T> // template prefix
void swapValues(T& var1, T& var2)
{
  T temp;
  temp = var;
  var1 = var2;
  var2 = temp;
}
```

### Template Prefix

- `template<typename T>`

- T can be replaced by any type
- In function definition body, T used like any other type
- Can use other than "T", but T is "traditional" usage

### Function Template Definition

- swapValues() function template is actually **large collection of definitions**
- Compiler only generates definitions when required
- Write one definition -> works for all types

### Calling a Function Template

`swapValues(int1, int2);`

- C++ compiler generates function definition for two int parameters using template
- Need not do anything special in call

### Multiple Type Parameters

`template<typename T1, typename T2>`

- Cannot have unused template parameters

## 2. Class Template

### Definition

```c++
// definition
template<typename T>
class Pair
{
public:
  Pair();
  Pair(T firstVal, T secondVal);
  void setFirst(T newVal);
  void setSecond(T newVal);
  T getFirst() const;
  T getSecond() const;
private:
  T first;
  T second;
}

// members
template<typename T>
Pair<T>::Pair(T firstVal, T secondVal)
{
  first = firstVal;
  second = secondVal;
}

template<typename T>
void Pair<T>::setFirst(T newVal)
{
  first = newVal;
}
```

### Declare objects

```c++
Pair<int> score;
Pair<char> seats;
```

### Uses

```c++
score.setFirst(3);
score.setSecond(0);
```

### Restrictions on Type Parameter

- **Assignment operator** must be well behaved
- **Copy constructor** must also well behaved
- If T involves pointers, then **destructor** must be suitable

### Templates and Inheritance

```c++
template<typename T>
class Triple : public Pair<T>
{
  ...
}
```

- Can derive from template or nontemplate class
- Derived class is then naturally a template class

---

