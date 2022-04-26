# 레이캐스트

레이 캐스트를 통해 다른 게임 오브젝트와 인터렉션을 하는 법을 배운다.

fps 플레이어의 눈앞의 사물을 다른 곳으로 옮기는 코드를 작성해 볼 것이다.

---

1. 강의에서 준비된 Standard Assets 를 임포트 한다. (원래 유니티 공식 패키지였으나, 유니티 버전이 업되면서 사라졌다.)

2. Assets/Standard Assets/Characters/FirstPersonCharacter/Prefabs/FPSController

   를 Hierarchy에 드래그 앤 드롭해서 추가한다.

3. 추가한 FPSController 에 이미 카메라가 있기 때문에 원래 있던 `Main Camera` 는 삭제한다.

4. 3D object -> Plane 을 추가한다.

5. FPSController 의 위치를 약간 바닥에서 위로 옮겨준다.

   (0, 1.5, 0)

6. 플레이어는 wasd 나 방향키를 이용해 움직일 수 있다.

7. Cube 를 새로 추가한다. 바닥에 잠겨있지 않고 바닥 위로 오게 살짝 올려준다.

8. FPSController 에 새로운 C# 스크립트 `RayInteraction` 을 추가한다.

9. Cube 에 Add Layer -> Interactable 이라는 레이어 추가. 레이어를 Interactable 로 설정.

10. 아래의 코드 적용 후, FPSController 의 스크립트 부분의 What Is Target 을 Interactable 로 설정.

11. ```c#
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    
    public class RayInteraction : MonoBehaviour
    {
        public LayerMask whatIsTarget;
        private Camera playerCam;
        public float distance = 100f;
        private Transform moveTarget;
        private float targetDistance;
    
        // Start is called before the first frame update
        void Start()
        {
            playerCam = Camera.main;
        }
    
        // Update is called once per frame
        void Update()
        {
            Vector3 rayOrigin = playerCam.ViewportToWorldPoint(new Vector3(0.5f, 0.5f, 0)); // 화면상의 점을 찍으면 월드 포지션으로 변환 (0.5면 중앙, 마지막 파라미터는 카메라의 깊이)
            Vector3 rayDir = playerCam.transform.forward; // 카메라의 방향 벡터를 받아옴
    
            Ray ray = new Ray(rayOrigin, rayDir); // 월드 포지션과 방향으로 레이를 생성
    
            Debug.DrawRay(rayOrigin, rayDir * distance, Color.green); // 레이를 그리고 색을 초록색으로 바꿈
    
            if (Input.GetMouseButtonDown(0))
            {
                RaycastHit hit; // 레이캐스트 결과를 받아올 변수
    
                if (Physics.Raycast(ray, out hit, distance, whatIsTarget)) // 광선을 쏴서 어떤 물체가 걸리면 true
                {
                    GameObject hitTarget = hit.transform.gameObject; // 걸린 물체를 받아옴
    
                    hitTarget.GetComponent<Renderer>().material.color = Color.red; // 걸린 물체의 색을 빨간색으로 바꿈
    
                    moveTarget = hitTarget.transform; // 걸린 물체의 위치를 받아옴
                    targetDistance = hit.distance; // 걸린 물체까지의 거리를 받아옴
    
                    Debug.Log(hit.collider.gameObject.name);
                    Debug.Log("Hit"); 
                }
            }
    
            if (Input.GetMouseButtonUp(0))
            {
                if (moveTarget != null)
                {
                    moveTarget.GetComponent<Renderer>().material.color = Color.white; // 물체의 색을 흰색으로 바꿈
                }
                moveTarget = null; // 물체를 떼면 다시 null로 바꿈
                Debug.Log("Release");
            }
    
            if (moveTarget != null)
            {
                moveTarget.position = ray.origin + ray.direction * targetDistance; // 시선이 닿는 곳에, 거리를 유지한채로 이동
            }
        }
    }
    
    ```

    