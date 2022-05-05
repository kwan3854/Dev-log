# Freelook 카메라 리깅

1. Lens -> Field Of View -> 60

2. Axis Control -> Y Axis -> Input Axis Value -> Invert 체크

3. X Axis -> Invert 체크 해제

4. Orbits -> Binding Mode -> World Space

5. Orbits ->

   - TopRig

     - Height: 3

     - Radius: 1.3

   - MiddleRig

     - Height: 1.5
     - Radius: 2

   - BottomRig

     - Height: 0.2
     - Radius: 1.3

6. TopRig

   - Body
     - X Damping: 0
     - Y Damping: 0
     - Z Damping: 0.5
   - Aim
     - Tracked Object Offset (0, 1.3, 0) (캐릭터의 발바닥이 아닌 상체를 중심으로 움직이게)
     - Screen X: 0.3 (약간 캐릭터를 왼쪽으로)
     - Screen Y: 0.6 (약간 캐릭터 위쪽을 보게)

7. MiddleRig

   - Body
     - X Damping: 0
     - Y Damping: 0
     - Z Damping: 0.5
   - Aim
     - Tracked Object Offset (0, 1.3, 0) (캐릭터의 발바닥이 아닌 상체를 중심으로 움직이게)
     - Screen X: 0.3 (약간 캐릭터를 왼쪽으로)
     - Screen Y: 0.65 (약간 캐릭터 위쪽을 보게)

8. BottomRIg

   - Body
     - X Damping: 0
     - Y Damping: 0
     - Z Damping: 0.5
   - Aim
     - Tracked Object Offset (0, 1.3, 0) (캐릭터의 발바닥이 아닌 상체를 중심으로 움직이게)
     - Screen X: 0.3 (약간 캐릭터를 왼쪽으로)
     - Screen Y: 0.7 (약간 캐릭터 위쪽을 보게)