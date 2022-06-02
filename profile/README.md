![로고](https://user-images.githubusercontent.com/79398383/171569945-46bfed25-cd98-47b4-82ca-e894839282f2.png)
# 📖 독서스터디 온/오프라인 모임 서비스, 북-잉
### [👉 북-잉 바로가기](https://book-ing.co.kr)
<br>

## 💡 북-잉 주요기능
### 🥺 꼭 만나서 해야하나요? ➔ 온라인 스터디를 위한 화상채팅!

### 🧐 오프라인 스터디 장소가 어디죠? ➔ 카카오 지도 스터디 장소 표시!

### ☎️ 모임별 연락은? ➔ 실시간 채팅 기능으로 편리하게!

### 📚 어떤 책을 공부할까요? ➔ 손쉬운 책 검색 및 등록으로 모두에게 알려주세요!

### 📝 스터디 끝! 정리는? ➔ 웹 에디터로 누구나 보기 쉽게 스터디노트 작성!
<br>


## 🖥 북-잉 프로젝트
### [Front-End Github](https://github.com/Book-ing/Book-ing_FE) / [Back-End Github](https://github.com/Book-ing/Book-ing_BE) 

### 🗓 프로젝트 기간
- 2022/04/22 ~ 2022/06/03

### 👨‍👨‍👦‍👦 북-잉 팀원
|                      Name                       |         Position          |        Contact         |
|:-----------------------------------------------:|:-------------------------:|:----------------------:|
|     [박민우](https://github.com/MinwooPark93)      | React / Assistance Leader | minwoopark93@gmail.com |
|        [김주탁](https://github.com/Taak-e)         |           React           |  kjtag0408@gmail.com   |
|      [유학선](https://github.com/hakseon-yoo)      |      NodeJs / Leader      |  gkrtjs2020@gmail.com  |
|      [김배승](https://github.com/MoingXTwice)      |          NodeJs           |  sckbs1314@naver.com   |
|        [서호진](https://github.com/ho-bolt)        |          NodeJs           |    gbolt@naver.com     |
| [정지현](https://www.instagram.com/design_hyeony/) |          Design           | gihyun684827@gmail.com |
|     [최서윤](http://yuni124.creatorlink.net/)      |          Design           |    em4715@naver.com    |
<br>

## 🎯 북-잉 트러블슈팅
<details>
<summary>1. 스터디 목록 조회</summary>
<div markdown="1">
<br>

저희 서비스 내 모임페이지에서 스터디 목록을 조회할 때, 서버에서 응답하는 속도가 스터디 개수에 비례하여 느려지는 현상을 발견하였습니다.<br>
확인해보니, 스터디 목록 조회 시, 카카오 지도에 위치를 표시하기 위한 좌표정보가 필요하여 스터디 생성 시 DB에 저장했던 주소정보를 구글 API를 통해 위도와 경도로 바꿔주는 과정을 거침에 따라 시간이 오래 걸리는 것을 확인하였습니다.<br>
해당 문제를 해결하기위해 스터디 컬렉션 내 위도와 경도 도큐먼트를 추가하고, 스터디 생성 시 입력받은 주소정보를 즉시 좌표정보로 변환하여 위도와 경도 도큐먼트에 값을 삽입하였습니다.<br>
이에 수정 전 10개 스터디를 조회하는데 있어 걸렸던 평균시간 2.5초에서 수정 후 0.5초 이내로 속도개선이 된 것을 확인할 수 있었습니다.<br>
</div>
</details>

<details>
<summary>2. 다른 네트워크 상의 P2P 연결</summary>
<div markdown="1">
<br>

WebRTC를 이용하여 1:N P2P 연결을 시도하던 중 테스트 단계에서 STUN서버를 이용하여 동일한 네트워크 내에 있는 사용자 간 연결은 성공했으나, 다른 네트워크 상에 있는 사용자와는 연결되지 않는 현상이 있었습니다.<br>
해당 문제에 대해 확인해보니, NAT에 막혀 각 피어 간 시그널링 불가능으로 인해 장치 정보를 불러오지 못했습니다. 기본적으로 P2P 연결 시 최적의 ICE를 찾으며 다른 클라이언트에게 갈 수 있는 최적의 네트워크를 찾는 것이 맞으나, STUN 서버를 거쳐도 NAT 뒤에 어떤 IP정보가 있는지 알 수 없는 상황이 있는 것이 원인이었습니다.<br>
따라서 해당 문제를 해결하기 위해 NAT 환경에서 릴레이하여 통신할 수 있는 TURN 서버를 자체적으로 구축하여 해당 문제를 해결하였습니다.<br>
TURN 서버는 인터넷 망에 위치하고 각 피어들이 사설망 안에서 통신하는데, 이 때 각 피어가 직접 통신하는 것이 아니라 릴레이 역할을 하는 TURN 서버를 통해 경유하여 다른 네트워크 사용자들끼리 연결가능 할 수 있다는 것을 알았습니다.<br>
</div>
</details>

<details>
<summary>3. 화면 공유</summary>
<div markdown="1">
<br>

WebRTC를 이용하여 화면공유 기능을 구현하던 중, 화면공유 스트림이 피어끼리 연결되지 못해 화면이 공유되지 않는 문제가 발생하였습니다.<br>
이 문제를 확인하기 위해 화면 스트림에 또 다른 소켓 아이디를 부여하여 시그널링 서버에서 Offer, Answer, Icecandidate를 주고 받으며, P2P 연결에 성공하였습니다. 하지만 화면공유 시, 각 피어의 트랙이 일정하지 않은 형태로 중복되어 여러개의 비디오와 음성이 출력되어 렌더링되는 문제가 발생하였습니다.<br>
해당 문제를 해결하기 위해 브레인 스토밍 하던 중, 최종 발표 준비까지의 시간이 여유치 않아, 최종 발표 이후 기능을 추가하기로 했습니다.<br>
</div>
</details>

