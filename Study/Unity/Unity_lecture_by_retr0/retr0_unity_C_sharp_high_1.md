# 유니티 이벤트 (1/2)

- C#의 이벤트와 델리게이트를 유니티가 사용하기 편하도록 랩핑 해 놓은 것.
- 이벤트는 발생시키는 쪽과 이벤트에 기능을 등록했던 쪽은 서로에게 관심이 없다.
- 이벤트에 기능을 등록했던 오브젝트는 기능이 언제 어떻게 발동될지 전혀 신경 쓰지 않음.
- 이벤트를 발동시키는 측도, 자기 이벤트에 어떤 것들이 등록되었는지 신경쓰지 않고 발동시킴.
- 서로 스파게티처럼 꼬이지 않는 장점이 있음.

## 예시

- 게임에는 흔히 도전과제라는 기능이 있다.
- 플레이어가 죽었을 때 도전과제를 달성했다고 해 주고, 플레이어를 다시 살려주는 상황을 가정

---

1. 유니티 스토어에서 Standard Assets 를 다운, 임포트 한다.

2. Assets/Standard Assets/Characters/ThirdPersonCharacter/Prefabs/ThirdPersonController 를 Hierarchy 에 드래그 앤 드롭

3. Plane 오브젝트를 추가해서 바닥을 만들어 줌

4. Cube 를 추가해서 바닥 아래로 내린 다음, 크기를 늘려 큐브를 바닥 아래 또 다른 바닥으로 만들어 준다.

   - 이 바닥에 플레이어가 떨어지면 죽는 것으로 만들 것이다.
   - Scale (20, 1, 20) 으로 했음

5. 새로운 Material 을 만들어 이름을 Red 로 하고 빨간색으로 하여 늘린 Cube 에 적용한다. (용암)

6. Cube 이름을 RedZone 으로 하고, Is Trigger 를 체크한다.

7. ThirdPersonController 에 PlayerHealth 라는 새로운 스크립트를 추가한다.

   - RedZone 에 닿으면 플레이어를 죽게 만들 용도

8. 다음과 같은 코드로 간단하게 플레이어를 죽게 만들 수 있다.

   ```c#
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   
   public class PlayerHealth : MonoBehaviour
   {
       void OnTriggerEnter(Collider other)
       {
           Debug.Log("죽었다!");
           Destroy(gameObject);
       }
   }
   
   ```

9. 이제 도전과제 해제 기능과, 게임 재시작 기능을 추가할 것이다.

10. UIManager 스크립트를 추가 (플레이어가 죽었다는 메세지 출력 용도)

11. Hierarchy 에 UI -> Text 추가

12. Cnavas / Text 오브젝트의 이름을 `Player State Text` 로 변경

13. Player State Text 의

    - Anchor Preset 에서 `Option` 키를 누른 체로 Stretch 옵션을 누른다.
    - Text/Paragraph/Alignment 를 Center, Middle 로 설정
    - Best Fit 옵션을 켜 줌
    - Text 내용을 지워서 빈 칸으로 만듦

14. Canvas 에 UIManager 를 붙임

15. ```c#
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    using UnityEngine.UI;
    
    public class UIManager : MonoBehaviour
    {
        public Text playerStateText;
        public void OnPlayerDead()
        {
            playerStateText.text = "You Die!";
        }
    }
    
    ```

16. Canvas 에 붙인 UI Manager 에 Player State Text 부분에 Player State Text 를 드래그 앤 드롭

17. 이제 OnPlayerDead() 가 실행되면 "You Die" 메세지가 출력 될 것임

18. 도전과제 시스템을 구현하기 위해 AchivementSystem.cs 를 새로 만듦.

19. ```c#
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    using UnityEngine.UI;
    
    public class AchivementSystem : MonoBehaviour
    {
        public Text achivementText;
    
        public void UnLockAchivement(string achivementName)
        {
            Debug.Log("도전과제 해제 - " + achivementName);
            achivementText.text = "도전과제 해제: " + achivementName;
        }
    }
    
    ```

20. Canvas 하단에 도전과제 텍스트를 위한 새로운 Text 추가.

    - 이름은 Achivement Text
    - 위 Player State Text 와 동일하게 Stretch.
    - Allignment 는 우측, 상단 
    - 폰트 사이즈는 60으로 키움
    - 텍스트 내용물은 지움

21. 위와 같은 방법으로 Canvas 에 AchivementSystem 을 붙이고, Achivement Text 를 드래그 앤 드롭

22. 마지막으로 GameManager 를 만들어서 캐릭터의 죽음을 감지하고 5초 뒤 게임을 재시작하게 만들어 보자.

23. 새로운 C# 스크립트 `GameManager` 를 만든다.

24. ```c#
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    using UnityEngine.SceneManagement;
    
    public class GameManager : MonoBehaviour
    {
        // 플레이어가 죽었을 때 5 초 뒤 게임을 재시작
        public void OnPlayerDead()
        {
            Invoke("Restart", 5f); // Invoke 함수는 인자로 넘겨준 함수를 지연 실행하는 시간을 지정하여 실행한다.
        }
    
        private void Restart()
        {
            SceneManager.LoadScene(0); // 0번은 현재 씬(지금 보고 있는 씬)을 의미
        }
    }
    
    ```

25. Hierarchy 에 GameManager 라는 이름의 빈 게임 오브젝트를 만들어 GameManager 스크립트를 붙인다.

26. 플레이어가 죽었을 때,

    - UIManager
    - AchivementSystem
    - GameManager

    이 세 가지 스크립트가 PlayerHealth 가 죽었다는 함수를 작동시킬때 자동으로 적절히 발동되어야 한다.

    - 만약 이벤트가 없으면 스파게티처럼 꼬이게 된다.

27. ```c#
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    
    public class PlayerHealth : MonoBehaviour
    {
        private void Dead()
        {
            Debug.Log("죽었다!");
            Destroy(gameObject);
        }
        void OnTriggerEnter(Collider other)
        {
            Dead();
        }
    }
    
    ```

28. 우리는 PlayerHealth 스크립트의 Dead() 라는 함수가 발동될 때, 동시에

    - UIManager 의 `OnPlayerDead()`
    - AchivementSystem 의 `UnLockAchivement()`
    - GameManger 의 `OnPlayerDead()`

    를 발동시키고 싶다.

    그렇기 위해서는 PlayerHealth 는 UIManager, AchivementSystem, GameManager 를 하드하게 알고 있어야 한다.

    -> (잘못된 방법)

    이렇게 하면 플레이어의 죽음에 관련된 모든 것들을 전부 PlayerHealth 가 알고 있어야 한다.

    예컨데 AchivementSystem 은 게임 개발이 거의 완료된 시점에 만드는데, PlayerHealth 는 게임 로직상에 있는 것이다.

    논리적으로 이런 게임 개발 플로우가 잘못되었다는 것을 알 수 있다.

29. 이벤트를 활용해서 문제를 해결하면 이 hard 하게 엮인, 스파게티 구조를 끊을 수 있다.