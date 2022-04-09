# 인스턴스화

## 인스턴스화(Instantiate) 란?

어떠한 대상을 실존하는 무언가로 찍어내는 것

미리 만들어진 오브젝트를 실시간으로 필요한 만큼 찍어낸다

보통 총알과 같은 물체를 실시간으로 찍어내는데 사용됨

프리펩을 가져와서 찍어내는 경우가 많음

## 코드

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Spawner : MonoBehaviour
{
    public Transform spawnPoisiton;
    public GameObject target;
    // Start is called before the first frame update
    void Start()
    {
        GameObject instance = Instantiate(target, spawnPoisiton.position, spawnPoisiton.rotation);

        instance.GetComponent<Rigidbody>().AddForce(0, 1000, 0);

        Debug.Log(instance.name);
    }
}

```

### 꼭 GameObject 타입으로만 Instantiate 해서 가져오지 않아도 된다

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Spawner : MonoBehaviour
{
    public Transform spawnPoisiton;
    public Rigidbody target;
    // Start is called before the first frame update
    void Start()
    {
        Rigidbody instance = Instantiate(target, spawnPoisiton.position, spawnPoisiton.rotation);

        instance.AddForce(0, 1000, 0);

        Debug.Log(instance.name);
    }
}

```

