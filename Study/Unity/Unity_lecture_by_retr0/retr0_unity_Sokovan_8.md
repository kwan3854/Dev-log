# 최종 빌드

## 배경음악 추가

1. mp3, wav 파일 등을 준비한다.
   - 유튜브에서 제공하는 저작권 무료 음악을 가져와도 좋다.
2. Project 창으로 드래그 앤 드롭해서 추가한다
3. BGM 을 재생할 오브젝트를 생성한다.
   - Hierarchy 창에서 `우클릭` -> `Create Empty`
   - 이름을 BGM 으로 변경
4. `Add Component` -> `Audio Source` (검색을 통해)
5. 오디오 파일을 `Audio Source` 의 `AudioClip` 으로 드래그 앤 드롭
6. Play On Awake, Loop 를 체크
7. Volume 값을 조절
8. 배경음악이 정상적으로 나오는지 테스트 플레이 해 본다.

## 게임 오버 되었을 때 플레이어가 못 움직이게 수정

1. Player.cs 파일을 연다

2. 게임이 끝났는지 여부를 알기 위해 GameManager 에 있는 `isGameOver` 변수가 필요하다

   - `public GameManager gameManager;` 라인을 추가해 GameManager 를 가져온다
   - Update 함수 내에서 if 문을 활용해 isGameOver 인지 확인하고 true 라면 return 해 준다.

3. ```c#
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   
   public class Player : MonoBehaviour
   {
       public GameManager gameManager;
       private float speed = 3f;
       private Rigidbody playerRigidbody; // 실제 존재하는 Rigidbody 를 가져다가 playerRigidbody 라는 이름을 붙여 사용
       // Start is called before the first frame update
       void Start() // 게임이 처음 시작되었을때 한번 실행됨
       {
           playerRigidbody = GetComponent<Rigidbody>(); // 제네릭 문법
       }
   
       // Update is called once per frame
       // 영화: 초당 24프레임, 모바일게임: 초당 30프레임, PC게임: 초당 60프레임, 콘솔게임: 초당 30프레임
       // 대략 1초에 60번 실행됨. 단, 컴퓨터 사양에 따라 다르다.
       // 즉, 1초에 몇 번 실행되는지 정해져 있지는 않다.
       void Update() // 화면이 한번 깜빡일 때 마다 한번씩 실행됨
       {
           if (gameManager.isGameOver == true)
           {
               return;
           }
           // 유저입력을 넣자.
           float inputX = Input.GetAxis("Horizontal");
           float inputZ = Input.GetAxis("Vertical");
           float fallSpeed = playerRigidbody.velocity.y;
   
           Vector3 velocity = new Vector3(inputX, 0, inputZ);
           velocity = velocity * speed;
   
           velocity.y = fallSpeed; // Y축 velocity 값을 원래 적용되었어야 할 원래 중력값으로 덮어쓰기 해 줌
   
           playerRigidbody.velocity = velocity;
       }
   }
   
   ```

4. Player 오브젝트의 Inspector 창에서 Game Manager 영역에 GameManager 오브젝트를 드래그 앤 드롭해서 적용
5. 테스트 해보면 게임 오버시 더이상 조작할 수 없음을 알 수 있다.

## 최종 빌드

1. `cmd` + `s` 로 씬을 저장 한다.
2.  `File` -> `Build Settings` -> `Build And Run`
3. 저장 위치를 지정 (프로젝트 파일과는 다른 위치로 하는 것이 좋다. 파일이 꼬일 수도 있다.)
   - 새로운 경로에 새로운 게임 폴더를 만들어서 그 안에 저장하는 것이 좋다
   - 바탕화면에 `My Sokovan ` 이라는 폴더를 만들어 그 안에 `Sokovan` 이라는 이름으로 저장했다.
4. 이제 게임이 플레이 된다.
5. 배포할때는 My Sokovan 폴더 자체를 압축해서 공유하면 된다.
   - 맥 유저는 .app 파일 하나만 생성되어 있다.
   - 윈도우 유저는 다른 폴더 하나가 더 생성되어 있을 것이다.