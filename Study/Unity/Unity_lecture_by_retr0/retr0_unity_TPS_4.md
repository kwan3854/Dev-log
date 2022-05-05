# Freelook 카메라 생성 + 데드존, 소프트존, 하드리밋

1. Main Camera 를 Brain Camera 로 만들어 준다.
   - Main Camera 에 Add Component -> Cine Machine -> Cinemachine Brain

2. 유니티 상단 툴바에서 Cinemachine -> Create FreeLook Camera -> 이름을 Follow Cam 으로 변경
   - FreeLook Camera 는 Virtual Camera 를 확장해서 만들어진 것
   - 3인칭 시점에 필요한 다양한 것들이 추가된 Virtual Camera 임
3. Follow Cam 의 Follow, Look At 대상을 Player Character 로 설정
   - Follow: 위치에 영향을 줌
   - Look At: 회전에 영향을 줌
   - 그리드를 통해서
     - 데드존
     - 소프트존
     - 하드존
     - 을 설정해서 카메라가 부드럽게 움직이는 정도를 조절할 수 있다.