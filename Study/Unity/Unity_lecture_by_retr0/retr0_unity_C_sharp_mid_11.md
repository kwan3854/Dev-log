# 상속

## 상속의 기본

1. Animal 이라는 새로운 cs 스크립트를 만든다.

2. ```c#
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   
   public class Animal
   {
       public string name;
       public float weight;
       public int year;
   
       public void Print()
       {
           Debug.Log(name + "| 몸무게: " + weight + "| 나이: " + year);
       }
   
       public float GetSpeed()
       {
           float speed = 100f/(weight * year);
   
           return speed;
       }
   }
   
   public class Dog: Animal
   {
       public void Hunt()
       {
           float speed = GetSpeed();
           Debug.Log(speed + " 의 속도로 달려가서 사냥했다.") ;
   
           weight = weight + 10f;
       }
   }
   
   public class Cat: Animal
   {
       public void Stealth()
       {
           Debug.Log("숨었다.");
       }
   }
   ```

3. ```c#
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   
   public class Test : MonoBehaviour
   {
       // Start is called before the first frame update
       void Start()
       {
           Cat nate = new Cat();
           nate.name = "Nate";
           nate.weight = 1.5f;
           nate.year = 3;
   
           Dog jack = new Dog();
           jack.name = "Jack";
           jack.weight = 5f;
           jack.year = 2;
   
   
           nate.Stealth();
           nate.Print();
   
           jack.Hunt();
           jack.Print();
       }
   }
   
   ```

## 접근 지정자

`public` , `protected` , `private`

코드를 적절하게 숨겨놓으면 밖에서 볼 때 간단하게 보인다.

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Animal
{
    public string name;
    public float weight;
    public int year;

    public void Print()
    {
        Debug.Log(name + "| 몸무게: " + weight + "| 나이: " + year);
    }

    protected float GetSpeed()
    {
        return CalcSpeed();
    }

    private float CalcSpeed()
    {
        return 100f/(weight * year);
    }
}

public class Dog: Animal
{
    public void Hunt()
    {
        float speed = GetSpeed();
        Debug.Log(speed + " 의 속도로 달려가서 사냥했다.") ;

        weight = weight + 10f;
    }
}

public class Cat: Animal
{
    public void Stealth()
    {
        Debug.Log("숨었다.");
    }
}
```

