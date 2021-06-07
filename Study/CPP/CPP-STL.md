# STL

## 1. Iterators

- 각각의 컨테이너는 자신만의 iterator 가 있다.
- iterator 의 추상화
  - 세세한 구현의 디테일을 숨김
  - 일관된 인터페이스 제공

### Manipulating Iterators

- '++'
- '--'
- '=='
- '!='
- '*' (deref)

### Iterating Vector Example

**iterator (classic way)**

```c++
vector<int> container;
vector<int>::iterator p;
for (p = container.begin(); p != container.end(); p++)
{
  cout << *p << " ";
}
```

**range based for loop (prefered way)**

```c++
vector<int> container;
for (const int& p : container)
{
  cout << p << " ";
}
```

### Iterator Classifications

- Forward iterators:

  ++ works on iterator

- Biderectional iterators:

  Both ++ and -- work on iterator

- Random access iterators:

  ++, --, and random access all work with iterator

### Constant and Mutable Iterators

- Dereferencing operator's behavior dictates
- **Constant iterator:**
  - `*` producs read-only version of element
  - `*p = <anything>; ` is illegal
- **Mutable iterator:**
  - `*p` can be assigned value
  - `*p` returns an *l-value*

## 2. Sequential Containers

### Vector

- push/pop at the back
- Random access iterator
  - `[]` operator
- Elements are stored contiguosly (like an array)
- Complexity
  - Random access - constant **O(1)**
  - Insertion or removal of elements at the end - amortized constatnt **O(1)**
  - Insertion or removal of elements - linear in the distance to the end of the vector **O(n)**

### Deque (Double-Ended QUEue)

- push/pop at the back
- Random access iterator
  - `[]` operator
- Element are not stored contiguously
- Complexity
  - Random access - constant **O(1)**
  - Insertion or removal of elements at the end or beginning - constant **O(1)**
  - Insertio or removal of elements - linear **O(n)**

### List

- push/pop at the fron and back
- Bidirectional iterator
- Element are connected through **doubly-linked list**
- Complexity
  - Insertion or removal of elements - constant **O(1)**
  - (Sequential) access - linear **O(n)**

## 3. Container Adapters stack and queue

- Container adapters are template classes

  - Implemented **"on top of"** other classes

- Example:

  **stack** template class by default implemented on top of deque template class

- Others:

  queue, priority_queue

### Specifying Container Adapters

can specify different underlying container

- Examples:

  - stack -> any sequence container
  - priority_queue -> default is vector could be others

- Example Code

  ```c++
  stack<int, vector<int> >
  ```

## 4. Associative Containers

- Simple database
- Store data
  - Each data item has key

### set/multiset

- **Sorted** set of objects
  - Set: unique object
  - Multiset: redundant objects
- Binary tree structure

### map/multimap

- **Sorted key/value pairs**
  - Map: unique key
  - Multimap: redundant keys
- Binary tree structure

### set Template Class

- Stores elements without repetition
  - 1st insertion places element in set
  - Each element is own key
- Insert/erase/find operations
  - In **O(log n)** complexity
- Bi-directional iterator

```c++
set<char> s;

s.insert('A');
s.insert('B');
s.insert('C');
s.insert('A'); // redundant data does not stored twice.
...

if (s.find('C') == s.end())
{
  // if 'C' not found
}

if (s.find('C') != s.end())
{
  // if 'C' found
}
```

### map Template Class

- Stores <key, value> pairs in a sorted order
  - template<T1, T2> struct pair {T1 first, T2 second};
  - The first and second are used for key and value, respectively.
- Insert/erase/find operations with the **key** (same as set)
  - In **O(log n)** complexity
- `[]` operator with the key
- Bi-directional iterator

```c++
map<string, string> planets;

// planets[Key] = Value
palnets["Mercury"] = "Hot planet";
palnets["Venus"] = "Atmosphere of sulfuric acid";
...
  
  
if (planets.find("Mercury") == planets.end())
{
  // if "Mercury" not found
}

if (planets.find("Ceres") == planets.end())
{
  // if "C" not found
}

map<string, string>::const_iterator iter;
for (iter = planets.begin(); iter != planets.end(); iter++)
{
  cout << iter->first << " - " << iter->second << endl;
}
```

## 4. Generic Algorithms

- Basic template functions

- Set of instructions for performing a task

- STL's algorithms in template functions:

  - Certain details provided only

    -> therefore considered "generic algorithms"

### The Generic find Function

```c++
vector<char>::const_iterator where;
where = std::find(line.begin(), line.end(), 'e');
```

### Sorting algorithm

```c++
std::sort(myvector.begin(), myvector.begin() + 4);

bool myfunction (int i, int j) { return (i<j); }
std::sort (myvector.begin() + 4, myvector.end(), myfunction);
```

---

