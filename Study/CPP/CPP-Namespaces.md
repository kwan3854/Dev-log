# Namespaces

## 1. Namespace?

- A collection of naem definitions
- What is name?
  - Class definitions
  - Variable declarations
- Programs use many classes, functions
  - Commonly have same names
  - Namespaces deal with this
  - Can be "on" or "off"

### using Directive

- `using namespace std;`
  - Makes all definitions in std namespace available
- Why might you NOT want this?
  - Can make coutm cin have non-standard
    - Perhaps a need to redefine cout, cin
  - Can redefine any others

### Namespace std

- Contains all names defined in many standard library files

- Example:

  `#include <iostream>`

  - Places all name definitions (cin, cout, etc.) into std namespace
  - Program doen't know names
  - Must specify this namespace for program to access names

### Global Namespace

- All code goes in some namespace
- Unless specified -> global namespace

## 2. Creating/using a Namespace

```c++
// Creating a namespace
namespace NameSpace_Name
{
  Somecode;
}
```

```c++
// All names in a namespace
using namespace NameSpace_Name;

// Some name in a namespace
using NameSpace_Name::somename;
```

---

