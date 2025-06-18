<h1 id="💡websocket">💡WebSocket</h1>
<h2 id="✅websocket이란">✅WebSocket이란?</h2>
<p>WebSocket이란 클라이언트와 서버 간의 지속적인 연결을 유지하고 양방향 토인이 가능한 프로토콜을 뜻한다.
요청-응답 모델을 사용하는 기존 http와 달리 웹소켓은 새로운연결을 반복적으로 설정하지 않고도 지속적으로 양방향 데이터 모델을 유지하게 된다.
채팅, 온라인, 주식시세 등을 예시로 들 수 있다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/371b8406-3d20-4526-8e2a-77392d931234/image.png" /></p>
<p>웹소켓도 서버 간의 연결을 하기 위해서는 최초에 한번은 http 요청을 보낸다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/561b736b-fe75-43d0-9f2b-6225ba2a2e0e/image.png" /></p>
<hr />
<h2 id="✅스프링에서-웹소켓을-사용하는-대표적인-두가지">✅스프링에서 웹소켓을 사용하는 대표적인 두가지</h2>
<h3 id="1-spring-websocket">1. Spring WebSocket</h3>
<ul>
<li>연결만 제공하고 핸들러는 직접 구현해야 한다. </li>
<li>단순 문자열(텍스트/바이너리)만 주고받기 때문에 순수 웹소켓이 더 가볍다
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/7b335056-c1a8-4553-bd16-398a8f35b9d1/image.png" /><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/9d4ae91b-1b80-464b-b7ca-e501a15c2d33/image.png" /></li>
</ul>
<hr />
<h3 id="2-spring-websocket--stomp">2. Spring WebSocket + STOMP</h3>
<ul>
<li>STOMP(Simple Text Oriented Messaging Protocol) : 텍스트 기반의 메시지 전송 프로토콜</li>
<li>특징 : 채팅방(채널) 구분, 구독/발행, 메시지 라우팅을 STOMP에서 자동 처리</li>
<li>WebSocket 위에서 동작하는 메시징 프로토콜. 메시지를 어떻게 주고받을지의 규칙이 정해져 있다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/61758b05-3557-4c84-84e3-89e699c8b2d0/image.png" /></li>
</ul>
<h3 id="2-1-stomp가-해주는-일">2-1. STOMP가 해주는 일</h3>
<ul>
<li>메시지 목적지(/topic/chat/room1, /queue/alert 등)를 헤더에 명시
-&gt; 어디로 라우팅해야 할 지 서버가 쉽게 파악</li>
<li>SUBSCRIBE, UNSUBSCRIBE, SEND 등 명령어로 구분
-&gt; 클라이언트가 &quot;구독만&quot;, &quot;메시지만 보내기&quot;, &quot;해제&quot; 등 역할 분리</li>
<li>헤더/바디 분리
-&gt; 인증, 타입, 우선순위 등 부가 정보(헤더)에 추가가능</li>
<li>브로커가 각 채널 구독자에게만 자동으로 메시지 분배 </li>
</ul>
<h3 id="2-2-stomp-메시지-구조">2-2. STOMP 메시지 구조</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/51444774-13a2-4752-ac16-a55969ede752/image.png" /></p>
<h3 id="2-3-stomp를-많이-쓰는-이유">2-3. STOMP를 많이 쓰는 이유</h3>
<ol>
<li>생산성과 유지보수성<ul>
<li>채팅방, 알림, 구독/취소, 브로드캐스트 등의 기능을 다 내장하고 있음</li>
</ul>
</li>
<li>코드가 간결하고 실수 가능성이 적음<ul>
<li>세션 관리, 채팅방 관리, 사용자 매핑, 목적지 라우팅 등을 다 코드로 직접 구현하게 되면 테스트/유지보수/협업 시 문제 발생</li>
</ul>
</li>
<li>확장성/변경 용이<ul>
<li>1:1 채팅방, 1:N 그룹방, 토픽별 알림 등을 STOMP는 경로만 바꾸면 적용 가능<ul>
<li>순수 웹소켓은 내부 코드 구조도 전부 변경해야함.</li>
</ul>
</li>
</ul>
</li>
</ol>
<h3 id="2-4-그래도-순수-websocket을-써야하는-상황은">2-4. 그래도 순수 WebSocket을 써야하는 상황은?</h3>
<ol>
<li>초고성능/초경량 통신이 필요할 때</li>
</ol>
<p>-&gt;게임서버,초저지연 실시간 서비스
-&gt;ms 단위의 레이턴시가 중요한 상황
-&gt;최대한 &quot;가공&quot;을 없애고 패킷 크기를 최소화하고 싶을 때
(STOMP는 텍스트 기반 프레임, 헤더 등 오버헤드가 조금이라도 있음)</p>
<ol start="2">
<li>메시지 구조를 직접 커스텀해야 할 때</li>
</ol>
<p>-&gt;사내 전용 메시지 포맷, 엔터프라이즈 표준, 바이너리 전용 등</p>
<ol start="3">
<li>아주 단순한 서비스</li>
</ol>
<p>-&gt;채널/구독/방 기능이 필요 없는 경우
-&gt;간단한 테스트용 데모 서버 등</p>
<hr />
<h2 id="✅순수-websocket-vs-stomp-websocket">✅순수 WebSocket VS STOMP WebSocket</h2>
<p> <img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/7aa38e29-7119-4064-9003-09b38e8a7583/image.png" /></p>