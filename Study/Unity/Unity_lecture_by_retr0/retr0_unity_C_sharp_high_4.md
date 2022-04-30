# 이벤트

## 이벤트란?

### 이벤트의 목적

우리가 이벤트를 사용하는 이유는 두 오브젝트간의 커플링(둘 사이의 높은 결합 문제) 을 방지하기 위해서이다.

### 이벤트의 구조

- Publisher
  - 이벤트를 발동 시키는 쪽
- Subscriber
  - Publisher 에 의해 발동되는 쪽

## 예시

### 개요

- 플레이어가 라운드 시작 전 부스터팩을 사용해 자신을 부스트할 수 있다.
- 부스터팩은 여러개를 중첩해서 사용할 수 있다.
- 플레이어는 중첩된 부스터팩에대해 모두 알 필요 없이 그저 발동시키만 한다.

### 구현 (1/2 델리게이트를 이용)

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Character : MonoBehaviour
{
    public delegate void Boost(Character targetCharacter);

    public Boost playerBoost;
    public string playerName = "KwanJoong";

    public float hp = 100;
    public float armor = 50;
    public float damage = 30;

    void Start()
    {
        playerBoost(this);
    }

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            playerBoost(this);
        }
    }
}

```

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Booster : MonoBehaviour
{
    public void HealthBoost(Character targetCharacter)
    {
        Debug.Log(targetCharacter.playerName + "의 체력을 강화합니다.");
        targetCharacter.hp += 10;
    }

    public void ArmorBoost(Character targetCharacter)
    {
        Debug.Log(targetCharacter.playerName + "의 방어력을 강화합니다.");
        targetCharacter.armor += 10;
    }

    public void DamageBoost(Character targetCharacter)
    {
        Debug.Log(targetCharacter.playerName + "의 공격력을 강화합니다.");
        targetCharacter.damage += 10;
    }

    void Awake()
    {
        Character player = FindObjectOfType<Character>();

        player.playerBoost += HealthBoost;
        player.playerBoost += ArmorBoost;
        player.playerBoost += DamageBoost;
    }
}

```

### 구현 (2/2 이벤트 키워드를 통해 델리게이트를 이벤트 용도로만 사용하게 제한)

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Character : MonoBehaviour
{
    public delegate void Boost(Character targetCharacter);

    public event Boost playerBoost; // 이벤트 키워드 추가
    public string playerName = "KwanJoong";

    public float hp = 100;
    public float armor = 50;
    public float damage = 30;

    void Start()
    {
        playerBoost(this);
    }

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            playerBoost(this);
        }
    }
}

```

이벤트 키워드를 추가했다.

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Booster : MonoBehaviour
{
    public void HealthBoost(Character targetCharacter)
    {
        Debug.Log(targetCharacter.playerName + "의 체력을 강화합니다.");
        targetCharacter.hp += 10;
    }

    public void ArmorBoost(Character targetCharacter)
    {
        Debug.Log(targetCharacter.playerName + "의 방어력을 강화합니다.");
        targetCharacter.armor += 10;
    }

    public void DamageBoost(Character targetCharacter)
    {
        Debug.Log(targetCharacter.playerName + "의 공격력을 강화합니다.");
        targetCharacter.damage += 10;
    }

    void Awake()
    {
        Character player = FindObjectOfType<Character>();

        player.playerBoost += HealthBoost;
        player.playerBoost += ArmorBoost;
        player.playerBoost += DamageBoost;

        // event 키워드를 사용하면 다음과 같은 경우들을 방지할 수 있다.

        // 이벤트를 덮어쓰기 하려는 경우
        // player.playerBoost = DamageBoost; // 에러 발생 : 이벤트는 덮어쓰기가 안됨

        // 이벤트를 Subscriber 측에서 실행하는 경우
        // player.playerBoost(player); // 에러 발생 : 이벤트는 Publisher 측에서만 실행할 수 있음
    }
}


```

event 키워드는 delegate 를 event 목적으로만 사용하게 만드는 제한자 이자 안전장치 역할을 함.