# 카메라 콜라이더

카메라 콜라이더를 통해 카메라가 벽에 파묻혀서 시야가 막히는 것을 방지할 수 있다.

1. Follow Cam 의 Cinemachine Free Look 컴포넌트의 가장 하단 Extensions 항목 -> Add Extension
   - Cinemachine Collider 를 선택
   - Ignore Tag: Player (카메라가 플레이어를 회피하면 이상하게 움직일 수 있음)
   - Smoothing Time: 0.1 (부드럽게 움직이는데 적용되는 지연시간)
   - Damping: 0.4 (값을 부드럽게 꺾는 정도)
   - Damping When Occluded: 0.28 (이미 콜라이더가 다른 콜라이더 내부로 들어갔을 때 벗어나기 위해 사용하는 값)