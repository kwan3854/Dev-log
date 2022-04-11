# 회전 포신

## 포신의 동작 메커니즘

- 마우스를 누르는 동안 각도가 변한다.
- 첫번째 클릭으로는 Yaw, 다음 클릭으로는 Pitch 가 조절된다.

## 구현

1. 씬을 `cmd` + `s` 를 통해서 저장한다.

2. 우리가 게임에서 사용할 포신이 될 Cube 게임 오브젝트를 생성한다. 이름은 "Ball Barrel Model" 로 한다.

3. 포신은 길쭉하게 생겼다

   - Scale 을 (0.2, 0.2, 2) 로 수정한다.

4. 포신의 색깔을 바꿔주기 위한 새로운 Material 을 생성하고, 이름을 "Barrel" 로 한다.

   - 색은 빨간색으로 한다.
   - 해당 Material 을 드래그 앤 드롭해서 적용한다.

5. Ball Barrel Model 의 Box collider 를 삭제한다. (이 오브젝트는 단순히 3D 그래픽을 표현하기 위한 오브젝트이기 때문)

6. 빈 게임 오브젝트를 생성 (포의 제어, 실제 중심 위치가 될 오브젝트임). 이름은 "Shooter Pivot" 으로 함

7. Ball Barrel Model 을 Shooter Pivot 의 하위로 넣는다.

8. `Pivot` `Local` 상태임을 확인하고 Scene 창에서 Ball Barrel Model 의 한쪽 끝이 Shooter Pivot 과 일치하게 만든다.

   - Shooter Pivot 의 Position
     - (0, 0, 0)
   - Ball Barrel Model 의 Position
     - (0, 0, 1)

9. 우리가 마우스 클릭을 통해 포신의 각도를 조절할 수 있어야 한다. 새로운 스크립트를 만든다.

10. Shooter Pivot 에게 `Add Component` -> `New Script` -> 이름을 `ShooterRotator`

11. ```c#
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    
    public class ShooterRotator : MonoBehaviour
    {
        private enum RotateState {
            Idle, Vertical, Horizontal, Ready
        }
    
        private RotateState state = RotateState.Idle;
        public float verticalRotateSpeed = 360f;
        public float horizontalRotateSpeed = 360f;
    
        void Update()
        {
            switch (state)
            {
                case RotateState.Idle:
                    if (Input.GetButtonDown("Fire1"))
                    {
                        state = RotateState.Horizontal;
                    }
                    break;
                case RotateState.Horizontal:
                    if (Input.GetButton("Fire1"))
                    {
                        transform.Rotate(new Vector3(0, horizontalRotateSpeed * Time.deltaTime, 0));
                    }
                    else if (Input.GetButtonUp("Fire1"))
                    {
                        state = RotateState.Vertical;
                    }
                    break;
                case RotateState.Vertical:
                    if (Input.GetButton("Fire1"))
                    {
                        transform.Rotate(new Vector3(-verticalRotateSpeed * Time.deltaTime, 0, 0));
                    }
                    else if (Input.GetButtonUp("Fire1"))
                    {
                        state = RotateState.Ready;
                    }
                    break;
                case RotateState.Ready:
                    break;
            }
        }
    }
    
    ```

### 마지막으로 의도한대로 잘 동작하는지 테스트 해 본다

