# 인터페이스

아이템 구현, 몬스터 구현, 플레이어 직업 구현 등에 많이 사용됨

상대방 아이템, 몬스터, 직업에 일일히 따로 구현 하지 않고 묶어서 구현할때 편리.

---

```c#
public interface IItem
{
  void Use();
}
```

```c#
public class GoldItem : MonoBehaviour, IItem
{
  public int goldAmount = 100;
  
  public void Use()
  {
    Debug.Log("골드를 얻었다!");
    
    Player player = FindObjectOfType<Player>();
    player.gold += goldAmount;
    
    gameObject.SetActive(false);
  }
}
```

```c#
public class HealthKitItem : MonoBehaviour, IItem
{
  public int restoreHealth = 50;
  
  public void Use()
  {
    Debug.Log("체력을 회복했다!");
    
    Player player = FindObjectOfType<Player>();
    player.hp += restoreHealth;
    
    gameObject.SetActive(false);
  }
}
```

```c#
// Player

void OnTriggerEnter2D(Collider2D other)
{
  IItem item = ohter.GetComponent<IItem>();
  
  if (item != null)
  {
    item.Use();
  }
}
```

