아자르 매칭 시스템
버킷 : 유저의 리퀘스트가 담김 → 매칭 : 점수를 매겨서 가능한 페어들을 만든다 → 페어의 점수 합산해서 sorting해서 빠져나가게함
페어 안에 중복된 유저가 들어있을수도 구롬 속해있던 다른 페어가 삭제됨
match route - match route step을 거쳐서 라이프사이클동안 어디를 거칠지를 결정? 한국 리전을 선택했을때 : 처음 5초는 한국, 지나면 global도 매칭시켜줌
오래 기다리면 점수를 높여줌(돈 냈을때), 돈 안내면 아무나 매칭해서 안기다리고 빨리 매칭됨
돈내는게 짱

match region - default, local, global 
match country 여러개 모여서 match region이 됨
match country override(segment별로(성별로 쪼갠다던가, 돈을 썼나로 쪼갠다던가...) 쪼개서 어떤 설정- match config(1:1) 을 적용할것이라는) 
match config -특정 세그먼트들을 어떻게 다룰것인가, 가산점 컨트롤, language bonus, hourly 설정, 매치 자동화(실시간으로 설정 조정 가능) ex) 로컬에서 튕겨서 못만나면 중간 단계에서 선호하는 super group 중에 하나로 보내서 기다리게 하고 진짜 안되면 글로벌로

큰 문제 : 돈을 쓴 사람이 자기가 만나고 싶은 local 여자를 못만남 →  
같은 리전이라도 country 설정이 다를수있음


Math server = Match~impl을 가지고 있는 모듈(boot만가지고있음) 껍데기
Match service = match serve의 api에 대한 interface 
Match ~ impl = service의 구현
Remote Match service = match service의 api 서버쪼 꾸현체

swipe
matchservice의 matchrandom이 불림 - randommatch request라는 클라이언트에서 dto를 보내줌 - matchoption(map 형태, 사용자에 대한 여러 정보?)  → 랜덤 매치 프로필(매치에 필요한것만) + match option 을 만들어줌 (= match request)
random match profile = 점수를 매길때를 위한 유저의 여러 정보를 가지고있음
match country override에서 설정을 보내준다. matchroute- match steps = 1. 1초동안 터키 남자를 만나고싶다 2. 터키 ㅏ무나 만난다 3. 5초동안 ㅋ터키와 중동 만난다..... 등의 스텝을 설정해두고 관리해줌

400 Milsec동안 기다리고 버켓에 다 채워지면 다음 리퀘스트부터는 다음 버켓에 차고 다 찬 버켓의 리퀘스트를 페어를 맺는다.
비동기로 클라이언트에게 미리 구독 아이디?(브로커 서버 채널 아이디)를 미리 알려주고 다 페어가 완료되면 다됐다고 알려줌

페어가 되면 matchinfo(peerprofile이 담겨있는, )를 넘겨주고 끄읕

normalRequest = 점수 판단 부분
matchRanfom = request 만드는부분

abort match = 잘 통화하다가 넘기기
cancel match = 아직 연결안됐는데 넘기기


모니터링 요원 - user level이 super로 되어있는 특별한 유저 - 

돈을 썼는데 원하는 사람이 안나와 - azar guarantee - 
