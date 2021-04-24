네트워크 중간고사 총정리

1. OSI7, 각 계층 이름, 각 계층에서 하는 역할, 각 계층에서 사용하는 데이터 단위

2. Circuit swtich

3. Packet switch

4. protocol

5. -internet standards

6. -network edge

7. access networks? -> end 시스템 부터 첫번째 라우터까지

8. physical media?

9. gateway

10. shared, dedicated

11. -DSL? HFC?

12. -Access net: cable network

13. -Access net: home network

14. -Enterprise access networks (Ethernet)

15. wireless LANs

16. wide-area wireless access (WAN)

17. packet transmission delay

18. guided media

19. unguided media

20. TP, UTP

21. radio link types

22. store and forward

23. -store and forward 하는데 걸리는 시간?

24. -store and forward 왕복 시간?

25. -pakcet switching: queueing delay, loss

26. Router 의 주요 기능 2가지

27. -circuit switch, packet switch

28. FDM, TDM

29. 패킷 스위칭이 무조건 좋냐? 장점과 단점, 패킷스위칭으로

30. 인터넷 구조에서 ISP 가 없다면?

31. ISP와 ISP 사이의 연결은?

32. -packet 딜레이와 Loss 가 어떻게 생기냐

33. packet nodal(전체) 딜레이 구하는 공식 (4개 더하는거)

34. -transmission delay 와 propagation delay 차이와 공식

35. 패킷 전체가 전부 transmission 되기도 전에 패킷 일부가 목표지점까지 이미 propagation 하는 경우 있을 수 있나? 그렇다면 먼저 도착한 패킷의 일부는 어떻게 되나?

36. -queueing delay 공식, 그래프로 나타내면 어떤 모양?

37. -실제 인터넷 딜레이 구하는 법 (명령어, 그리고 그 명령어 동작방식)

38. 패킷 로스 생기는 이유?

39. 패킷 로스 생기면 다시 보내주는건 몇계층에서 담당?

40. Throughput?

41. Throughput bottleneck link에 대해 설명

42. 실제 인터넷에서 누가 throughput bottleneck 이 되는가?

43. link layer의 두가지 sublayer?

44. 2계층에서 peer to peer 신뢰성 있는 연결성 보장하고, 3계층이 목적지 까지의 연결성 보장하면 자연스래

45. end to end 연결 신뢰성 보장 되는거 아님? (예외의 예시 대봐)

46. half duplex, full duplex 비교

47. link layer의 많은 기능들은 어디에 어떻게 구현되어있나?

48. -link layer는 어떤것들의 콤비네이션인가? (3개)

49. frame은 어떤것들로 이루어 져 있나? frame의 구조 설명

50. -이 frame을 이용해 어댑터들 사이에서 frame 전달하는 과정 설명

51. 에러 디텍션 (EDC 비트를 만드는 방법 3가지) 설명

52. 그 중에서 2계층에서 사용하는 것과 3계층에서 사용하는 것은?

53. CRC 설명 알파벳 3개 등장

54. 뒤에 붙는 crc 비트 구하는 방법?

55. -link 에는 정보를 주고받는 방법에서 두가지 종류가 있다. 두가지 각각 설명

56. 그 중에서 Multiple access protocol은 어느 link 방식에서 적용되는가?

57. -Multiple access protocol은 알고리즘이 동작하는 방식에 한가지 특징이 있다. 그 특징으로 운용되는 이유.

58. -이상적인 multiple access protocol의 조건은? R bps의 대역폭일때, 4가지 설명

59. -MAC protocol의 세가지 방식? 각각 설명하고 가장 많이 쓰는 방식 하나 골라

60. TDMA?

61. FDMA?

62. collision은 어떤 방식에서 발생하는 건가?

63. 어떻게 감지하는가?

64. 어떻게 그것을 recover하는가?

65. CSMA? CSMA/CD? CSMA/CA?

66. CSMA에서 Collision 이 발생하는 원리?

67. CSMA/CD 는 유선과 무선에서 각각 구현 난이도가 어떤가?

68. -CSMA/CD 의 알고리즘 작동 순서(단계)로 나누어 설명

69. back off 에 대해 설명 왜 필요한지.

70. back off 시 시간 계산에 대해 설명

71. CSMA/CD, CSMA/CA 각각 무선과 유선 어디에 사용되는가? 유선과 무선 프로토콜 예시 하나씩 대봐

72. IP 주소에서 MAC 주소 알아내는 프로토콜?

73. MAC 주소는 하드웨어의 어느부분에 정해져있는것인가? 그 하드웨어 1개당 몇개 가질 수 있는가?

74. IP 주소는 총 몇 비트?

75. IP 주소는 몇 계층에서 사용됨?

76. 맥 주소와 아이피 주소의 차이 (사용되는 네트워크 범위 관점에서)

77. 맥주소는 어떻게 결정되는가? 애초에 처음에?

78. 내가 컴퓨터를 들고 다른 장소에 가서 네트워크에 연결하면 MAC 주소는 변하는가?

79. 내가 컴퓨터를 들고 다른 장소에 가서 네트워크에 연결하면 IP 주소는 변하는가?

80. ARP란 무엇인가?

81. ARP 동작 과정?

82. 중요한점: ARP는 다른 네트워크간에도 동작하는가? 

83. 다른 LAN으로 라우팅은 어떻게 하는가?

84. LAN 내부의 호스트는 게이트웨이의(라우터) MAC 주소는 어떻게 아는가?

85. 라우터는 어떻게 목적지 호스트의 MAC 주소를 아는가?

86. 이더넷의 물리적 연결 방식 2가지가 있다. 둘다 각각 설명하고 둘 중 어떤게 최근에 사용되는지 말해라.

87. 이더넷 프레임의 구조를 설명해라. 가장 앞에 붙는 것이 무엇인지 어떤 구조인지 말해라.

88. 이더넷의 두 가지  단점이자 특징 설명.

89. 이더넷의 MAC 프로토콜은 어떤 방식을 사용해서 구현되는지

90. 이더넷 스탠다드의 핵심은 이것이다. 서로다른 physical layer를 사용하면 어떻게 되나?

91. IP 주소를 두 부분으로 나눌 수 있다. 각각 나눠서 설명

92. IP 주소의 클래스 종류 설명, 각각 앞에 붙는 숫자가 뭔지(구별법), 각각 총 ip 주소에서의 비중

93. A클래스는 몇개의 기관이 배정 받을 수 있는가?

94. A클래스에서 각 주소에서 할당 가능한 최대 호스트 갯수?

95. B클래스는 몇개의 기관이 배정 받을 수 있는가?

96. 서브넷이란?

97. 서브넷 마스크? 각 클래스별로 서브넷 마스크는 어떻게 생겼는가?

98. 네트워크 레이어에서 라우터가 하는 가장 중요한 2가지 기능?

99. 각 두가지 기능을 한국말로 뭐라고 하는가?

100. virtual circuit과 datagram network 는 각각 어떤 특징?(차이)

101. 라우터 내부의 모습?

102. IP는 데이터가 잘 갔는지 보장하는가? 그렇지 않다면 누가 그걸 보장하는가? 보장하지는 않지만 어떻게는 해주는가?

103. 그 방식을 영어로 뭐라고 하는가?

104. 같은 호스트가 패킷을 여러개 보내면 모두 같은 라우팅 경로로 가는가?

105. forwarding 과 routing 의 과정 설명 머릿속으로 그림을 그려가며

106. forwarding table은 어떤식으로 생겼는가?

107. forwarding table은 어떤 방식으로 매칭을 하는가? 만약 범위가 깔끔하게 떨어지지 않고 좀 너저분하면? (용어)

108. Internet (datagram)의 특징 3가지

     1. 누구와 누가 통신을 하는지
     2. 어떤 매체를 사용하는지
     3. end 시스템이 어떤지, 또 그것으로 인해 어떤 방식으로 네트워크 전송하는지

109. 라우터에서 forwarding table은 어디에 위치해 있어야 하는가?

110. 라우터에서 input 될때와 output 될때의 과정 그림으로 생각

111. switching 하는데는 2가지 방식이 있다.(세가지 인데 하나는 안배웠음) 설명

112. 스위치에서 output port에서 스케줄링 되는 방식? (6주 2회차 27슬라이드 매우중요)

113. 스위치 intput port 에서 딜레이가 생기는 원인? (영어 용어 있음) 그거 설명, 딜레이가 길어지면 무슨일이 일어날 수 있음?

114. IP 프로토콜? ICMP 프로토콜?

115. IP 데이터그램의 구조

116. IP 데이터그램에서 헤더의 길이?

117. IP 데이터그램에서 data 부분의 정체?

118. IP 데이터그램이 조각화될 때가 있다. 그 원인?(용어있음) 다시 합치는건 어떻게?

119. IP 데이터그램 fragmentation 과정?

     나누기 전 길이와 나누고 나서의 길이는 어떻게 되는가?

     나누는 길이의 기준점은? 118답과 같음

     ID?

     fragflag?

     offset?

120. 서브넷이란? 서브넷의 갯수를 세라.

121. 같은 서브넷 안에서는 네트워크 주소가 어떤가?

122. CIDR란? CIDR 생긴 이유?

123. IP주소는 어떻게 알아낼 수 있는가?

124. 123의 답이 동작하는 방식?

125. (중요)123의 역할?

126. 123의 답은 IP주소를 알려주는 것 외에 다양한 것을 리턴한다. IP이외에 리턴하는것 3가지

127. 만약 서브넷을 8등분 하고 싶다면 서브넷 마스크를 어떤식으로 씌워야 함?

128. 만약 네트워크를 옮겼는데 이전과 같은 네트워크 주소를 사용하고싶다면? (용어)

129. NAT란? 이것 사용하는 이유?

130. 외부 네트워크에서 사설 아이피 알 수 있는가?

131. 알 수 없다면, 어떻게 둘 간에 통신하는가? 어떤것 이용?

132. 외부 네트워크와 사설 아이피 호스트 둘 간의 통신법에 문제가 있다. 그 문제와 그 문제 해결법 (용어)

133. ICMP?

134. ICMP 이용해서 딜레이 특정하는 법? (마지막 노드까지)

135. IPv6 에서 터널링이란?

136. 터널링의 과정?
