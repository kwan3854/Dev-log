# IK(Inverse Kinematic)

- 애니메이션 적용 시, 어떤 지점을 중심으로 관절의 위치를 역 계산
- 물체의 위치에 맞춰서 시선이나 팔의 위치가 정해졌으면 좋겠을 때 주로 사용됨

---

1. 새로운 3D 오브젝트 Sphere 를 추가하고 이름을 Ball 로 함

2. Scale (0.2, 0.2, 0.2) 로 줄임

3. IK 를 사용해서 유니티짱이 Ball 을 잡았으면 좋겠음

4. unitychan 의 PlayerController.cs 파일을 편집

   ```c#
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   
   public class PlayerController : MonoBehaviour
   {
       public Transform target;
   
       // 애니메이션의 파라미터가 어떻게 되었는지 상관없이 코드단에서는 파라미터만 조정한다.
       public Animator anim;
   
       // Start is called before the first frame update
       void Start()
       {
           
           
       }
   
       // Update is called once per frame
       void Update()
       {
           if (Input.GetButtonDown("Jump"))
           {
               anim.SetTrigger("Jump"); // 파라미터에서 설정한 이름과 완전히 동일해야 한다.
           }
   
           float verticalInput = Input.GetAxis("Vertical");
           float horizontalInput = Input.GetAxis("Horizontal");
   
           anim.SetFloat("Speed", verticalInput);
           anim.SetFloat("Horizontal", horizontalInput);
       }
   
       void OnAnimatorIK()
       {
           // 왼손을 물체로 이동
           anim.SetIKPositionWeight(AvatarIKGoal.LeftHand, 1.0f); // Weight값(0 ~ 1)만큼 우선해서 IK 가 적용된다.
           anim.SetIKRotationWeight(AvatarIKGoal.LeftHand, 1.0f);
   
           anim.SetIKPosition(AvatarIKGoal.LeftHand, target.position);
           anim.SetIKRotation(AvatarIKGoal.LeftHand, target.rotation);
   
           // 시선을 물체로 이동
           anim.SetLookAtWeight(1.0f);
           anim.SetLookAtPosition(target.position);
       }
   }
   
   ```

5. Layer 들이 IK 를 받을 수 있게 설정해야 함
   - 모든 레이어들에 IK Path 를 체크함 (표정레이어는 체크할 필요없음)