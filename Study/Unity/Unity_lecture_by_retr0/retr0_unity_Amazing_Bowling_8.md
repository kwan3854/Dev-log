# 카메라 추적

1. Main Camera -> Camera -> Projection -> Orthographic 으로 바꾼다.

   - Perspective: 소실점이 존재. 멀리있는건 작게, 가까이 있는 것은 크게 보임
   - Orthographic: 원근감이 없음. 가까이 있는 물체나 멀리 있는 물체나 똑같이 보임.
   - 본 게임은 독특한 분위기를 위해 Orthographic 을 사용할 것이다.
     - 길건너 친구들이라는 게임이 Orthographic 을 이용한 대표적 게임이다.

2. Main Camera 의 Transform 을 다음과 같이 조정한다.

   - Position: (0, 5, 0)
   - Rotation: (40, -60, 0)
   - Scale: (1, 1, 1)

3. 카메라를 어떤 오브젝트에 딱 붙여버리면 그 오브젝트가 전혀 보이지 않는다. 우리가 원하는 것은 그것보다 좀 더 카메라를 띄워서 배치하는 것이다.

   - 빈 게임 오브젝트를 생성한다.
   - 이름을 Rig 으로 한다. (이 오브젝트가 추 역할을 하고, 카메라는 여기에 질질 끌려다니게 된다)
   - 이제 오브젝트를 따라다니는 것은 Rig이다.

4. Rig 의 Transform 을 다음과 같이 조정한다.

   - Position: (0, 0, 0)
   - Rotation: (40, -60, 0)
   - Scale: (1, 1, 1)

5. Main Camera 를 Rig 의 자식으로 넣는다.

6. Main Camera 의 Transform 을 다음과 같이 조정한다.

   - Position: (0, 0, -65)
   - Rotation: (0, 0, 0)
   - Scale: (1, 1, 1)

7. Main Camera 에 Size 가 있다.

   - 현재 메인 카메라는 Orthographic 상태이다.
   - Orthographic 상태에서 Size 는 카메라에 한번에 담을 수 있는 게임 세상의 크기를 의미한다.

8. Rig 에 새로운 스크립트를 만들어 붙인다.

   - 이름은 CamFollow

9. ```c#
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   
   public class CamFollow : MonoBehaviour
   {
       public enum State
       {
           Idle, Ready, Tracking
       }
   
       private State state
       {
           set
           {
               switch (value)
               {
                   case State.Idle:
                       targetZoomSize = roundReadyZoomSize;
                       break;
   
                   case State.Ready:
                       targetZoomSize = readyShotZoomSize;
                       break;
   
                   case State.Tracking:
                       targetZoomSize = trackingZoomSize;
                       break;
               }
           }
       }
   
       private Transform target;
   
       public float smoothTime = 0.2f;
   
       private Vector3 lastMovingVelocity;
       private Vector3 targetPosition;
   
       private Camera cam;
       private float targetZoomSize = 5f;
   
       private const float roundReadyZoomSize = 14.5f;
       private const float readyShotZoomSize = 5f;
       private const float trackingZoomSize = 10f;
   
       private float lastZoomSpeed;
   
       void Awake()
       {
           cam = GetComponentInChildren<Camera>();
           state = State.Idle;
       }
   
       private void Move()
       {
           targetPosition = target.transform.position;
           Vector3 smoothPosition = Vector3.SmoothDamp(transform.position, targetPosition, ref lastMovingVelocity, smoothTime);
   
           transform.position = smoothPosition;
       }
   
       private void Zoom()
       {
           float smoothZoomSize = Mathf.SmoothDamp(cam.orthographicSize, targetZoomSize, ref lastZoomSpeed, smoothTime);
   
           cam.orthographicSize = smoothZoomSize;
       }
   
       private void FixedUpdate()
       {
           if (target != null)
           {
               Move();
               Zoom();
           }
       }
   
       public void Reset()
       {
           state = State.Idle;
       }
   
       public void SetTarget(Transform newTarget, State newState)
       {
           target = newTarget;
           state = newState;
       }
   }
   
   ```

   