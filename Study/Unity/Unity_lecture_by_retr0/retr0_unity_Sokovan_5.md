# 충돌 처리

ItemBox 를 밀어서 EndPoint 에 도달했을 때 인식할 필요가 있다.

## Tag

1. EndPoint 프리펩의 Inspector 창을 본다.
2. 상단에 Tag 라는 부분이 있다.
3. Add Tag 하고, `EndPoint` 라는 태그를 추가한다.
4. 다시 EndPoint 프리펩 Inspector 창으로 돌아와 Tag 를 EndPoint 로 지정한다.
5. 모든 EndPoint 오브젝트들에 적용이 되었는지 확인한다.

## 충돌감지

1. ItemBox 가 Tag를 감지하여 충돌을 판단해야 한다
2. ItemBox 프리펩에 Inspector 창에서 Add Component 버튼을 눌러 새로운 C# 스크립트를 생성한다(ItemBox 라는 이름으로)

유니티 게임 오브젝트는 자신이 충돌했다는 사실은 알지 못한다.

그러나 유니티는 Trigger(뚫고 지나갈 수 있는) 인 collider 와 충돌할때 `OnTriggerEnter` 라는 메세지를 충돌한 오브젝트들에게 뿌린다.

그리고 그 메시지를 받으면 자동으로 `OnTriggerEnter` 함수가 실행시킨다.

반대로 뚫고 지나갈 수 없는 collider 와 충돌했을때 시작되는 함수는 `OnCollisionEnter` 함수를 사용한다.

### 충돌감지 함수

- `OnTrigger~` : 트리거인 콜라이더(뚫을 수 있는 것) 과 충돌할 때 자동으로 실행
  - `OnTriggerEnter` : 충돌을 한 그 순간 실행됨
  - `OnTriggerExit` : 충돌했다가 떼어질 때 실행됨
  - `OnTriggerStay` : 충돌해 있는 동안, 붙어있는 동안 실행됨
- `OnCollision~` : 뚫을 수 없는 함수와 충돌할 때 자동으로 실행

### 코드

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class ItemBox : MonoBehaviour
{
    private Renderer myRenderer;
    public Color touchColor;
    private Color originalColor;
    // Start is called before the first frame update
    void Start()
    {
        // GetComponent 를 이용해 자신이 가진 컴포넌트 중 Renderer 를 가져옴.
        myRenderer = GetComponent<Renderer>();
        originalColor = myRenderer.material.color; // 원래 컬러 백업
    }

    // Update is called once per frame
    void Update()
    {
        
    }

    void OnTriggerEnter(Collider other)
    {
        // EndPoint 이외에도 뚫고 지나갈 수 있는 collider 가 있을 수 있으므로 Tag 를 통해 필터링
        if (other.tag == "EndPoint")
        {
            myRenderer.material.color = touchColor; // 충돌 시 색 변경
        }
    }

    void OnTriggerExit(Collider other)
    {
        if (other.tag == "EndPoint")
        {
            myRenderer.material.color = originalColor; // 떨어질 때 원래 색으로 변경
        }
    }

    void OnTriggerStay(Collider other)
    {
        if (other.tag == "EndPoint")
        {
            myRenderer.material.color = touchColor; // 충돌 시 색 변경
        }
    }
}

```

- ItemBox 프리펩 Inspector 창에서 Touch Color 를 적당한 색 (eg. 파랑색) 으로 지정.

## 코드 수정

ItemBox 가 너무 잘 밀려서 게임 플레이가 어렵다.

ItemBox 프리펩 Inspector 창에서 Rigidbody 영역을 보면

- Mass: 질량
- Drag: 마찰력

등이 있는데 Drag 를 10으로 조정해 마찰력을 증가시킨다.