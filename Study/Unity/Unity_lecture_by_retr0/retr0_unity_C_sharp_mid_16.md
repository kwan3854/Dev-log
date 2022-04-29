# 프로퍼티



## 프로퍼티를 사용하지 않는 방법

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PointManager : MonoBehaviour
{
    private int point = 0;

    public void SetPoint(int newPoint)
    {
        if (newPoint < 0)
        {
            point = 0;
        }
        else if (newPoint > 10000)
        {
            point = 10000;
        }
        else
        {
            point = newPoint;
        }
    }

    public int GetPoint()
    {
        Debug.Log(point);
        return point;
    }
}

```

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Test : MonoBehaviour
{
    public PointManager pointManager;
    void Start()
    {
        pointManager.SetPoint(100);

        int myPoint = pointManager.GetPoint();
        Debug.Log("현재 포인트: " + myPoint);

        pointManager.SetPoint(-1000); // 필터링 됨
        myPoint = pointManager.GetPoint();
        Debug.Log("현재 포인트: " + myPoint);

    }
}

```

getter 와 setter 를 만드는 것이 번거롭다.

## 프로퍼티를 사용한 방법

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PointManager : MonoBehaviour
{
    // 프로퍼티를 사용해 point 변수의 getter 와 setter 구현
    public int point
    {
        get
        {
            Debug.Log(m_point);
            return m_point;
        }
        set
        {
            if (value < 0)
            {
                m_point = 0;
            }
            else if (value > 10000)
            {
                m_point = 10000;
            }
            else
            {
                m_point = value;
            }
        }
    }

    private int m_point = 0;
}

```

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Test : MonoBehaviour
{
    public PointManager pointManager;
    void Start()
    {
        pointManager.point = 100;
        Debug.Log("현재 점수 " + pointManager.point);

        pointManager.point = -100;
        Debug.Log("현재 점수 " + pointManager.point);
    }
}

```

## 프로퍼티의 활용

- 몬스터의 갯수가 실시간으로 계속 변화하는 상황
  - 이 변화하는 값을 실시간으로 반영하게 하고 싶음
- 개발자가 실수로 임의로 몬스터의 숫자를 변경하지 않도록 하고싶음

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class MonsterManager : MonoBehaviour
{
    public int count
    {
        get
        {
            Monster[] monsters = FindObjectsOfType<Monster>();

            return monsters.Length;
        }
    }
}

```

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Test : MonoBehaviour
{
    public MonsterManager monsterManager;
    void Start()
    {
        Debug.Log(monsterManager.count);
        // monsterManager.count = 10; // 임의의 값 변경 불가능 setter 가 없으므로
    }
}

```

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Monster : MonoBehaviour
{
    // Monster 오브젝트의 갯수를 세는 용도이므로 현재 아무 구현도 필요없음
}

```

