# 포탄

1. 새로운 3D 오브젝트 Sphere 를 생성하고 이름을 Ball 로 한다.

2. 새로운 Material 을 생성하고 이름을 Ball 로 한다. 색은 파란색으로 하고 적용

3. Ball 의 Sphere Collider 중,  Is Trigger 를 체크 해제한다. (충돌되어 공이 튕겨나가는 것 방지. 우리는 공이 튕겨나가는것이 아닌 터져버리는 것을 원한다.)

4. 공은 중력의 영향을 받아야 한다. 따라서 Rigid Body 를 추가한다.

5. 공이 어딘가에 닿았을 때 폭발 효과를 줘야 한다.

   - EffectExamples 폴더에 가면 아까 넣은 이펙트들이 있다.
   - Fire & Explosion Effects -> Prefabs -> PlasmaExplosionEffect 를 드래그 앤 드롭해서 가져온다.

6. 폭발 효과가 계속 반복해서 일어나는 것을 볼 수 있다. 우리는 효과가 원할때 단 한번만 재생되는것을 원한다.

   - PlasmaExplosionEffect 오브젝트와 그 하위 오브젝트들을 `Shift` 키를 이용해 모두 선택하고 Inspector 창에 Particle System 하위의 Looping 옵션을 체크 해제, Play On Awake 체크 해제.

7. 이제 폭발시에 효과음이 필요하다.

8. PlasmaExplosionEffect 에 `Add Component` -> `Audio` -> `Audio Source` -> `AudioClip` 에서 `Explosion_00`효과음 선택, Play On Awake 를 체크 해제.

   - 이제 폭발 효과가 재생될때 같이 폭발 효과음이 재생된다.

9. Assets 폴더에 `Prefabs` 라는 새로운 폴더를 만들어 준다.

10. PlasmaExplosionEffect 를 Prefabs 폴더로 드래그 앤 드롭해서 프리팹으로 만들어 준다.

11. 아까 만들었던 PlasmaExplosionEffect 오브젝트를 삭제하고 다시 프리펩에서 Ball 오브젝트 하위로 넣어준다.

12. Ball 과 PlasmaExplosionEffect 의 Position 을 0, 0, 0 으로 변경

13. PlasmaExplosionEffect 의 Game Object Active 를 체크 해제 해 준다. (Inspector 창에서 오브젝트 이름 왼쪽에 있는 체크 표시)

    - 평소에는 보이지 않게 하다가 우리가 나중에 Ball 오브젝트가 파괴될때 Active 상태로 바꾸어서 보이게 할 것이다.

14. Ball 오브젝트에게 `Add Component` -> 새로운 스크립트 -> 이름은 Ball

15. Ball 오브젝트를 Prefabs 폴더로 드래그 앤 드롭해서 프리팹으로 만들어 준다.

    - Ball 오브젝트는 발사할 때 마다 찍어내서 사용해야 하기 때문

16. 바닥을 만들어 주어야 한다.

    - 새로운 `3D 오브젝트` -> `Plain` -> Position 은 (0, 0, 0) -> 이름을 Plane 으로 변경
    - Plain 의 색깔을 위해 Material 생성 -> 색은 오렌지 색
    - 드래그 앤 드롭해서 적용

17. Ball 스크립트를 열어서 작성한다.

18. ```c#
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    
    public class Ball : MonoBehaviour
    {
        public ParticleSystem explosionParticle;
        public AudioSource explosionAudio;
    
        public float maxDamage = 100f;
        public float explosionForce = 1000f;
        public float LifeTime = 10f; // 맵 밖으로 나가면 파괴되지 않는 문제가 생길 수 있으므로 수명을 제한한다.
        public float explosionRadius = 20f;
    
        void Start()
        {
            Destroy(gameObject, LifeTime);
        }
    
        void OnTriggerEnter(Collider other)
        {
            // 부모 오브젝트를 파괴하면 자식 오브젝트도 파괴된다.
            // 공을 먼저 파괴시키고, 자식 게임오브젝트인 PlasmaExplosionEffect 가 파괴되지 않게 해야 한다.
            // 따라서 공 오브젝트를 파괴시킬 때, PlasmaExplosionEffect 를 Ball 의 자식에서 해제시킨다.
            explosionParticle.transform.parent = null;
    
            explosionParticle.Play();
            explosionAudio.Play();
    
            Destroy(explosionParticle.gameObject, explosionParticle.duration); // 재생이 다 되면 파괴한다. (메모리 낭비 방지)
            Destroy(gameObject);
        }
    }
    
    ```

19. Ball 오브젝트의 Ball(Script) 에 (prefab 에 모두 적용되게)

    - Explosion Particle 에 PlasmaExplosionEffect 를 드래그 앤 드롭
    - Explosion Audio 에 PlasmaExplosionEffect 를 드래그 앤 드롭