# 추상 클래스

인터페이스와 비슷하다.

인터페이스는 완전히 껍데기 이지만, 추상 클래스는 어느정도 구현을 포함 할 수 있다.

`abstract` 키워드를 이용한다,

## 코드 예시

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public abstract class BaseMonster : MonoBehaviour
{
    public float damage = 100f;

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            Attack();
        }
    }
    public abstract void Attack();
}

```

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Goblin : BaseMonster
{
    public override void Attack()
    {
        Debug.Log("고블린이 한 캐릭터를 공격합니다. 공격력: " + damage);
    }
}

```

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Orc : BaseMonster
{
    public override void Attack()
    {
        Debug.Log("오크가 광역 공격합니다. 공격력: " + damage);
    }
}

```

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Test : MonoBehaviour
{
    public BaseMonster[] monsters;

    void Start()
    {
        for (int i = 0; i < monsters.Length; i++)
        {
            Debug.Log(monsters[i].gameObject.name);
        }
    }
}

```

