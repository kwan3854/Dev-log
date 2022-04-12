# 프롭 + 데미지 시스템

## 파괴가 되고, 튕거나가는 물체들을 만들어준다.

1. 새로운 3D 오브젝트 Cube 를 생성하고 이름을 Small Prop 이라고 한다.

2. Rigid Body 추가

3. `Add Component` -> 새로운 C# 스크립트 -> 이름 Prop

4. ```c#
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   
   public class prop : MonoBehaviour
   {
       public int score = 5;
       public ParticleSystem explosionParticle;
       public float hp = 10f;
       public void TakeDamage(float damage)
       {
           hp -= damage;
           if (hp <= 0)
           {
               ParticleSystem instance = Instantiate(explosionParticle, transform.position, transform.rotation);
               Destroy(instance.gameObject, instance.main.duration); // 재생이 다 되면 파괴한다. (메모리 낭비 방지)
   
               // 매 스테이지에 굉장히 많은 프롭들이 필요한데, 매번 새로 생성, 파괴 하면 렉이 많이 걸림.
               // 그래서 화면에 보이지 않게 임시로 꺼 두고, 스테이지 전환시 재사용한다.
               gameObject.SetActive(false);
           }
       }
   }
   
   ```

5. `Assets/EffectExamples/Fire & Explosion Effects/Prefabs/SmallExplosion` 을 Hierarchy 에 드래그 앤 드롭

6. SmallExplosion 과 그 하위 오브젝트들을 모두 선택한 채로 Looping, Play On Awake 해제.

7. 다시 Hierarchty 창에서 SmallExplosion 을 우리가 만든 Prefabs 폴더로 드래그 앤 드롭 해서 프리팹으로 만듦

8. 만든 프리펩에 Add Component -> Audio Source

9. Play On Awake 체크 해제

10. 적당한 효과음을 적용 (Explosion_03)

11. 다시 Prop.cs 코드로 돌아가서 오디오가 재생되게 만듦

12. ```c#
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    
    public class Prop : MonoBehaviour
    {
        public int score = 5;
        public ParticleSystem explosionParticle;
        public float hp = 10f;
        public void TakeDamage(float damage)
        {
            hp -= damage;
            if (hp <= 0)
            {
                ParticleSystem instance = Instantiate(explosionParticle, transform.position, transform.rotation);
                Destroy(instance.gameObject, instance.main.duration); // 재생이 다 되면 파괴한다. (메모리 낭비 방지)
    
                AudioSource explosionAudio = GetComponent<AudioSource>();
                explosionAudio.Play();
                instance.Play();
    
                // 매 스테이지에 굉장히 많은 프롭들이 필요한데, 매번 새로 생성, 파괴 하면 렉이 많이 걸림.
                // 그래서 화면에 보이지 않게 임시로 꺼 두고, 스테이지 전환시 재사용한다.
                gameObject.SetActive(false);
            }
        }
    }
    
    ```

13. Hierarchy 상의 SmallExplosion 을 제거

14. Small Prop 의 Prop 스크립트쪽에 Explosion Particle 에 Assets/Prefabs 폴더안의 SmallExplosion 을 드래그 앤 드롭

15. Small Prop 역시 드래그 앤 드롭해서 Prefab 으로 만듦

## 폭발 구현

### 구현 원리

- 폭발 위치에서부터 explosion radius 만큼 구를 그린다.
- 그 범위 안에 있는 Prop 들에게 Take Damage 를 발동시킨다.
- 수많은 Collider 들 중에서 어떻게 Prop 을 구별할까?
  - 오브젝트를 구별하는데 사용되는 두가지 방법이 있다.
    - Tag: 일반적인 경우, 1대1 비교만 가능
    - Layer: 물리 처리에 주로 사용, 한번에 여러개 필터링 가능
    - 이 폭발 구현에서는 layer 를 사용한다.

### 구현

1. Small Prop 의 오브젝트에서 `Layer` -> `Add Layer` -> 새로운 User Layer `Prop` 을 추가하고 적용한다.

2. Ball.cs 를 수정한다.

3. `public ParticleSystem explosionParticle;` 라인을 추가

   ```c#
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   
   public class Ball : MonoBehaviour
   {
       public LayerMask whatIsProp;
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

4. Ball 오브젝트의 스크립트 What Is Prop 부분에 필터링을 여러개를 걸 수 있으나, Prop 만 선택한다.

5. 다시 Ball.cs 로 돌아가서

6. ```c#
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   
   public class Ball : MonoBehaviour
   {
       public LayerMask whatIsProp;
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
           Collider[] colliders = Physics.OverlapSphere(transform.position, explosionRadius, whatIsProp);
   
           for (int i = 0; i < colliders.Length; i++)
           {
               Rigidbody targetRigidbody = colliders[i].GetComponent<Rigidbody>();
   
               if (!targetRigidbody)
                   continue;
   
               targetRigidbody.AddExplosionForce(explosionForce, transform.position, explosionRadius);
   
               Prop prop = targetRigidbody.GetComponent<Prop>();
   
               if (!prop)
                   continue;
   
               prop.TakeDamage(CalculateDamage(targetRigidbody.position));
           }
   
   
   
           // 부모 오브젝트를 파괴하면 자식 오브젝트도 파괴된다.
           // 공을 먼저 파괴시키고, 자식 게임오브젝트인 PlasmaExplosionEffect 가 파괴되지 않게 해야 한다.
           // 따라서 공 오브젝트를 파괴시킬 때, PlasmaExplosionEffect 를 Ball 의 자식에서 해제시킨다.
           explosionParticle.transform.parent = null;
   
           explosionParticle.Play();
           explosionAudio.Play();
   
           Destroy(explosionParticle.gameObject, explosionParticle.duration); // 재생이 다 되면 파괴한다. (메모리 낭비 방지)
           Destroy(gameObject);
       }
   
       private float CalculateDamage(Vector3 targetPosiion)
       {
           Vector3 explosionToTarget = targetPosiion - transform.position;
           float distance = explosionToTarget.magnitude;
           float edgeToCenterDistance = explosionRadius - distance;
           float percentage = edgeToCenterDistance/explosionRadius;
           float damage = maxDamage * percentage;
           damage = Mathf.Max(0f, damage);
           return damage;
       }
   }
   
   ```

7. small prop 을 여러개 만들어 테스트 해 본다.

