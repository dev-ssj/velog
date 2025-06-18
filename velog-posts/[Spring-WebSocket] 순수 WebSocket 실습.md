<h1 id="1-순수-웹소켓-채팅방-생성">1. 순수 웹소켓 채팅방 생성</h1>
<h2 id="1-웹소켓-의존성-추가">1. 웹소켓 의존성 추가</h2>
<pre><code>implementation 'org.springframework.boot:spring-boot-starter-websocket’</code></pre><h2 id="2-backendprojectpurewebsocket-handler-패키지-안에-chatwebsockethandlerjava-생성">2. backendproject.purewebsocket. handler 패키지 안에 ChatWebSocketHandler.java 생성</h2>
<pre><code class="language-java">  package com.example.backendproject.purewebsocket.handler;

  import com.example.backendproject.purewebsocket.dto.ChatMessage;
  import com.fasterxml.jackson.databind.ObjectMapper;
  import org.springframework.web.socket.CloseStatus;
  import org.springframework.web.socket.TextMessage;
  import org.springframework.web.socket.WebSocketSession;
  import org.springframework.web.socket.handler.TextWebSocketHandler;

  import java.lang.runtime.ObjectMethods;
  import java.util.Collections;
  import java.util.HashSet;
  import java.util.Set;

  public class ChatWebSocketHandler extends TextWebSocketHandler {

      //sessions : 현재 웹소켓에 연결된 클라이언트들(WebSocketSession)을 관리하는 Set컬렉션
      //           이 컬렉션에서 WebSocketSession 객체를 추가하거나 제거하면서 접속적인 유저를 추적
      //HashSet을 사용하는 이유 : Set은 중복을 허용하지 않음 -&gt; 같은 클라이언트 세션이 여러번 저장되는 것 방지 가능
      //Collections.synchronizedSet() : 기본적으로 HashSet은 스레드에 안전하지 않음. 웹소켓 서버는 멀티스레드 환경에서 작동하므로, 여러 사용자가 동시에 접속하거나 연결을 끊는 경우가 발생 -&gt; 동시성 문제
      //이 때 synchronizedSet()를 사용해 동기화된 안전한 Set을 만들어 동시성 문제를 예방한다.
      private final Set&lt;WebSocketSession&gt; sessions = Collections.synchronizedSet(new HashSet&lt;&gt;());

      //서버에서 메시지를 보내면 JSON형식이다. 그것을 자바 객체로 변환해주고, 다시 자바객체를 JSON 문자열로 바꿔주는 역할
      private final ObjectMapper objectMapper =new ObjectMapper();

      //ctrl + o : 오버라이드 단축키
      //클라이언트가 웹소켓 서버에 접속했을 때 호출
      //WebSocketSession session : 서버에 접속한 id.
      @Override
      public void afterConnectionEstablished(WebSocketSession session) throws Exception {
          super.afterConnectionEstablished(session);

          //서버에 접속한 id를 sessions에 넣어줌(관리하기 위해)
          sessions.add(session);

          System.out.println(&quot;접속된 클라이언트 세션 ID = &quot; + session.getId());
      }

      //클라이언트가 보낸 메세지를 서버가 받았을 때 호출(즉, 사용자가 메시지를 보냈을 떄)
      @Override
      protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
          super.handleTextMessage(session, message);

          // JSON 문자열 -&gt; 자바객체 변환
          ChatMessage chatMessage = objectMapper.readValue(message.getPayload(), ChatMessage.class);

          for(WebSocketSession s: sessions){
              if(s.isOpen()){
                  //자바 객체 -&gt; JSON 문자열
                  s.sendMessage(new TextMessage(objectMapper.writeValueAsString(chatMessage)));
              }
          }
      }

      //클라이언트의 연결이 끊겼을 때 호출
      @Override
      public void afterConnectionClosed(WebSocketSession session, CloseStatus status) throws Exception {
          super.afterConnectionClosed(session, status);

          //연결이 끊기면 session 삭제
          sessions.remove(session);
      }
  }
</code></pre>
<h2 id="3-backendprojectpurewebsocket-dto패키지-내에-chatmessagejava-생성">3. backendproject.purewebsocket. dto패키지 내에 ChatMessage.java 생성</h2>
<pre><code class="language-java">package com.example.backendproject.purewebsocket.dto;


import lombok.Getter;

@Getter
public class ChatMessage {

    private String message; //메시지
    private String from;    //발신자
}
</code></pre>
<h2 id="4-backendprojectpurewebsocketconfig-패키지-내에-websocketconfigjava-생성">4. backendproject.purewebsocket.config 패키지 내에 WebSocketConfig.java 생성</h2>
<pre><code class="language-java">package com.example.backendproject.purewebsocket.config;

import com.example.backendproject.purewebsocket.handler.ChatWebSocketHandler;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.config.annotation.EnableWebSocket;
import org.springframework.web.socket.config.annotation.WebSocketConfigurer;
import org.springframework.web.socket.config.annotation.WebSocketHandlerRegistry;

@Configuration
@EnableWebSocket
public class WebSocketConfig implements WebSocketConfigurer {

    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
                //ws-chat 앤드포인트로 요청을 보낼 수 있는지 결정하는 보안 정책 설정
        registry.addHandler(new ChatWebSocketHandler(),&quot;/ws-chat&quot;)
                .setAllowedOriginPatterns(&quot;*&quot;); //모든 브라우저에서 접근 가능
    }
}</code></pre>
<h2 id="5-포스트맨에서-확인-postman--new---websocket">5. 포스트맨에서 확인 postman -&gt;new -&gt; websocket</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/097042dd-4052-4b5e-b8d4-4552f4dbcd4b/image.png" /></p>
<h2 id="6-wslocalhost8080ws-chat-작성후-connect">6. ws://localhost:8080/ws-chat 작성후 connect</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/755f1ec0-7c54-4957-bebe-c7d059738f48/image.png" /><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/7f476a04-d176-4f35-8fbc-42afd4d28cce/image.png" /></p>
<h2 id="7-json-문자열로-메시지-보내보기">7. JSON 문자열로 메시지 보내보기</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/28f5e081-b478-412f-bbec-d32c0d76db01/image.png" /></p>
<h2 id="8-프론트엔드와-연동--static-폴더-안에-purechat1html-작성">8. 프론트엔드와 연동 : static 폴더 안에 purechat1.html 작성</h2>
<pre><code class="language-html">&lt;!DOCTYPE html&gt;
&lt;html lang=&quot;ko&quot;&gt;
&lt;head&gt;
    &lt;meta charset=&quot;UTF-8&quot;&gt;
    &lt;title&gt;Pure WebSocket Chat&lt;/title&gt;
    &lt;style&gt;
        body { font-family: 'Segoe UI', sans-serif; background: #f7f8fa; }
        .container {
            width: 400px; margin: 60px auto; background: #fff; padding: 32px 30px;
            border-radius: 14px; box-shadow: 0 2px 8px rgba(0,0,0,0.07);
        }
        h2 { text-align: center; color: #2c3e50; margin-bottom: 24px; }
        #chatArea {
            width: 100%; height: 250px; border: 1px solid #aaa;
            border-radius: 8px; margin-bottom: 18px; overflow-y: auto;
            background: #fafdff; padding: 10px 7px; font-size: 15px;
        }
        .row { display: flex; gap: 10px; align-items: center; margin-bottom: 13px; }
        input[type=&quot;text&quot;] {
            box-sizing: border-box; border: 1px solid #ccc; border-radius: 6px;
            font-size: 15px; padding: 9px; outline: none; background: #f9fafd;
            transition: border 0.2s;
        }
        input[type=&quot;text&quot;]:focus { border-color: #4078c0; background: #fff; }
        #user { width: 110px; }
        #msg { flex: 1; min-width: 0; }
        button {
            background: #4078c0; color: white; font-weight: bold;
            border: none; border-radius: 6px; padding: 10px 20px;
            font-size: 15px; cursor: pointer; transition: background 0.2s;
        }
        button:hover { background: #285690; }
        .btn-disconnect {
            background: #eee; color: #285690; font-weight: bold;
        }
        .btn-disconnect:hover { background: #e0e8f5; }
        .sysmsg { color: #666; font-style: italic; margin: 7px 0 3px 0;}
        .msgrow { margin-bottom: 3px;}
        .from { font-weight: bold; color: #4078c0;}
        .hidden { display: none; }
    &lt;/style&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;div class=&quot;container&quot;&gt;
    &lt;h2&gt;Pure WebSocket Chat&lt;/h2&gt;

    &lt;!-- 로그인 영역 --&gt;
    &lt;div class=&quot;row&quot; style=&quot;margin-bottom: 15px;&quot;&gt;
        &lt;input type=&quot;text&quot; id=&quot;user&quot; placeholder=&quot;닉네임&quot;&gt;
        &lt;button onclick=&quot;connect()&quot;&gt;Connect&lt;/button&gt;
        &lt;button class=&quot;btn-disconnect&quot; onclick=&quot;disconnect()&quot;&gt;Disconnect&lt;/button&gt;
    &lt;/div&gt;

    &lt;!-- 채팅 영역 (처음엔 숨김) --&gt;
    &lt;div id=&quot;chatWrapper&quot; class=&quot;hidden&quot;&gt;
        &lt;div id=&quot;chatArea&quot;&gt;&lt;/div&gt;
        &lt;div class=&quot;row&quot;&gt;
            &lt;input type=&quot;text&quot; id=&quot;msg&quot; placeholder=&quot;메시지&quot; onkeydown=&quot;if(event.key==='Enter'){sendMessage();}&quot;&gt;
            &lt;button onclick=&quot;sendMessage()&quot;&gt;Send&lt;/button&gt;
        &lt;/div&gt;
    &lt;/div&gt;
&lt;/div&gt;

&lt;script&gt;
    let ws = null;

    function connect() {
        const user = document.getElementById(&quot;user&quot;).value;
        if (!user) {
            alert(&quot;닉네임을 입력하세요!&quot;);
            return;
        }

        ws = new WebSocket(&quot;/ws-chat&quot;);

        ws.onopen = function () {
            showSysMsg('Connected!');
            document.getElementById(&quot;chatWrapper&quot;).classList.remove(&quot;hidden&quot;);
        };

        ws.onmessage = function (event) {
            const msg = JSON.parse(event.data);
            showMessage(msg.from, msg.message);
        };

        ws.onclose = function () {
            showSysMsg('Disconnected');
            document.getElementById(&quot;chatWrapper&quot;).classList.add(&quot;hidden&quot;);
        };
    }

    function disconnect() {
        if (ws) {
            ws.close();
            ws = null;
        }
    }

    function sendMessage() {
        const user = document.getElementById(&quot;user&quot;).value;
        const msg = document.getElementById(&quot;msg&quot;).value;
        if (!user || !msg) {
            alert(&quot;닉네임과 메시지를 모두 입력하세요!&quot;);
            return;
        }
        ws.send(JSON.stringify({ from: user, message: msg }));
        document.getElementById(&quot;msg&quot;).value = &quot;&quot;;
    }

    function showMessage(from, message) {
        const chatArea = document.getElementById(&quot;chatArea&quot;);
        chatArea.innerHTML += `&lt;div class=&quot;msgrow&quot;&gt;&lt;span class=&quot;from&quot;&gt;${from}:&lt;/span&gt; ${message}&lt;/div&gt;`;
        chatArea.scrollTop = chatArea.scrollHeight;
    }

    function showSysMsg(msg) {
        const chatArea = document.getElementById(&quot;chatArea&quot;);
        chatArea.innerHTML += `&lt;div class=&quot;sysmsg&quot;&gt;${msg}&lt;/div&gt;`;
        chatArea.scrollTop = chatArea.scrollHeight;
    }
&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;</code></pre>
<h2 id="9-프로젝트-하위에-htmlcontrollerjava-생성">9. 프로젝트 하위에 htmlController.java 생성</h2>
<pre><code class="language-java">package com.example.backendproject.purewebsocket;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HtmlController {
    @GetMapping(&quot;/&quot;)
    public String index(){
        return &quot;redirect:/purechat2.html&quot;;
    }
}</code></pre>
<h2 id="10localhost8080-접속">10.localhost:8080 접속</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/618796eb-be97-4b2b-bfe5-daab61fcb0e5/image.png" /></p>
<h1 id="2-데이터베이스-컨테이너-연동하여-채팅방-만들기">2. 데이터베이스 컨테이너 연동하여 채팅방 만들기</h1>
<h2 id="1-chatwebsockethandlerjava-수정">1. ChatWebSocketHandler.java 수정</h2>
<pre><code class="language-java">package com.example.backendproject.purewebsocket.handler;

import com.example.backendproject.purewebsocket.dto.ChatMessage;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.web.socket.CloseStatus;
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;
import org.springframework.web.socket.handler.TextWebSocketHandler;

import java.lang.runtime.ObjectMethods;
import java.util.Collections;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ConcurrentMap;

public class ChatWebSocketHandler extends TextWebSocketHandler {

    //sessions : 현재 웹소켓에 연결된 클라이언트들(WebSocketSession)을 관리하는 Set컬렉션
    //           이 컬렉션에서 WebSocketSession 객체를 추가하거나 제거하면서 접속적인 유저를 추적
    //HashSet을 사용하는 이유 : Set은 중복을 허용하지 않음 -&gt; 같은 클라이언트 세션이 여러번 저장되는 것 방지 가능
    //Collections.synchronizedSet() : 기본적으로 HashSet은 스레드에 안전하지 않음. 웹소켓 서버는 멀티스레드 환경에서 작동하므로, 여러 사용자가 동시에 접속하거나 연결을 끊는 경우가 발생 -&gt; 동시성 문제
    //이 때 synchronizedSet()를 사용해 동기화된 안전한 Set을 만들어 동시성 문제를 예방한다.
    private final Set&lt;WebSocketSession&gt; sessions = Collections.synchronizedSet(new HashSet&lt;&gt;());

    //서버에서 메시지를 보내면 JSON형식이다. 그것을 자바 객체로 변환해주고, 다시 자바객체를 JSON 문자열로 바꿔주는 역할
    private final ObjectMapper objectMapper =new ObjectMapper();

    //방과 방 안에 있는 세션을 관리하는 객체
    private final Map&lt;String, Set&lt;WebSocketSession&gt;&gt; rooms = new ConcurrentHashMap&lt;&gt;();

    //ctrl + o : 오버라이드 단축키
    //클라이언트가 웹소켓 서버에 접속했을 때 호출
    //WebSocketSession session : 서버에 접속한 id.
    @Override
    public void afterConnectionEstablished(WebSocketSession session) throws Exception {
        super.afterConnectionEstablished(session);

        //서버에 접속한 id를 sessions에 넣어줌(관리하기 위해)
        sessions.add(session);

        System.out.println(&quot;접속된 클라이언트 세션 ID = &quot; + session.getId());
    }

    //클라이언트가 보낸 메세지를 서버가 받았을 때 호출(즉, 사용자가 메시지를 보냈을 떄)
    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
        super.handleTextMessage(session, message);

        // JSON 문자열 -&gt; 자바객체 변환
        ChatMessage chatMessage = objectMapper.readValue(message.getPayload(), ChatMessage.class);


        String roomId = chatMessage.getRoomId();    //클라이언트에게 받은 메세지에서 roomID를 추출
        if(!rooms.containsKey(roomId)){ //방을 관리하는 객체에 현재 세션이 들어가는 방이 있는지 확인
            rooms.put(roomId, ConcurrentHashMap.newKeySet());   //없으면 새로운 방을 생성
        }

        //방이 있으면 기존의 방에 session만 추가
        rooms.get(roomId).add(session);


        for(WebSocketSession s: rooms.get(roomId)){
            if(s.isOpen()){
                //자바 객체 -&gt; JSON 문자열
                s.sendMessage(new TextMessage(objectMapper.writeValueAsString(chatMessage)));

                System.out.println(&quot;전송된 메시지 = &quot; + chatMessage.getMessage());
            }
        }
    }

    //클라이언트의 연결이 끊겼을 때 호출
    @Override
    public void afterConnectionClosed(WebSocketSession session, CloseStatus status) throws Exception {
        super.afterConnectionClosed(session, status);

        //연결이 끊기면 session 삭제
        sessions.remove(session);

        //연결이 해제되면 소속되어 있는 방에서 제거
        for(Set&lt;WebSocketSession&gt; room : rooms.values()){
            room.remove(session);
        }
    }
}
</code></pre>
<h2 id="2-의존성-추가">2. 의존성 추가</h2>
<pre><code>runtimeOnly 'com.mysql:mysql-connector-j'
implementation 'org.springframework.boot:spring-boot-starter-data-jpa'</code></pre><h2 id="3-applicationproperties-변경">3. application.properties 변경</h2>
<p>spring.datasource.url=jdbc:mysql://localhost:3307/backend?serverTimezone=Asia/Seoul&amp;characterEncoding=UTF-8&amp;rewriteBatchedStatements=true
spring.datasource.username=root
spring.datasource.password=1234</p>
<p>#mysql8.0 기준 하이버네이트에게 어떤 DB를 쓰는지 명시해주기
spring.jpa.database-platform=org.hibernate.dialect.MySQL8Dialect
spring.jpa.hibernate.ddl-auto=update</p>
<ul>
<li>기존에 만든 database 컨테이너 정보 기재</li>
</ul>
<h2 id="4-room-패키지-추가-후-코드작성-→-controller-service-entity-repository">4. room 패키지 추가 후 코드작성 → controller, service, entity, repository</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/49ba4083-2b56-431a-904f-433fc3a83b14/image.png" /></p>
<h2 id="5-static-폴더에-purechat2html-추가">5. static 폴더에 purechat2.html 추가</h2>
<pre><code class="language-html">&lt;!DOCTYPE html&gt;
&lt;html lang=&quot;ko&quot;&gt;
&lt;head&gt;
    &lt;meta charset=&quot;UTF-8&quot;&gt;
    &lt;title&gt;Room-Based WebSocket Chat&lt;/title&gt;
    &lt;style&gt;
        body { font-family: 'Segoe UI', sans-serif; background: #f7f8fa; }
        .container {
            width: 540px;
            margin: 60px auto;
            background: #fff;
            padding: 32px 30px;
            border-radius: 14px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.07);
        }
        h2 { text-align: center; color: #2c3e50; margin-bottom: 24px; }
        .row { display: flex; gap: 10px; align-items: center; margin-bottom: 13px; }
        input[type=&quot;text&quot;] {
            box-sizing: border-box;
            border: 1px solid #ccc;
            border-radius: 6px;
            font-size: 15px;
            padding: 9px;
            outline: none;
            background: #f9fafd;
            transition: border 0.2s;
            width: 100%;
            max-width: 130px;
        }
        input[type=&quot;text&quot;]:focus {
            border-color: #4078c0;
            background: #fff;
        }
        button {
            background: #4078c0; color: white; font-weight: bold;
            border: none; border-radius: 6px; padding: 10px 20px;
            font-size: 15px; cursor: pointer; transition: background 0.2s;
        }
        button:hover { background: #285690; }
        .btn-disconnect {
            background: #eee; color: #285690; font-weight: bold;
        }
        .btn-disconnect:hover { background: #e0e8f5; }
        #chatArea {
            width: 100%; height: 250px; border: 1px solid #aaa;
            border-radius: 8px; margin-bottom: 18px; overflow-y: auto;
            background: #fafdff; padding: 10px 7px; font-size: 15px;
        }
        .sysmsg { color: #666; font-style: italic; margin: 7px 0 3px 0; }
        .msgrow { margin-bottom: 3px; }
        .from { font-weight: bold; color: #4078c0; }
        .hidden { display: none; }
        #roomList { margin-bottom: 20px; }
        .room-item {
            padding: 6px 10px;
            border: 1px solid #ccc;
            border-radius: 6px;
            margin-bottom: 5px;
            cursor: pointer;
        }
        .room-item:hover {
            background: #e8f0ff;
        }
    &lt;/style&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;div class=&quot;container&quot;&gt;
    &lt;h2&gt;Pure WebSocket Chat Room ADD&lt;/h2&gt;

    &lt;!-- 방 목록 표시 --&gt;
    &lt;div id=&quot;roomList&quot;&gt;
        &lt;strong&gt;방 목록:&lt;/strong&gt;
        &lt;div id=&quot;rooms&quot;&gt;&lt;/div&gt;
    &lt;/div&gt;

    &lt;!-- 닉네임 입력 및 disconnect --&gt;
    &lt;div class=&quot;row&quot; id=&quot;nicknameRow&quot;&gt;
&lt;!--        &lt;input type=&quot;text&quot; id=&quot;user&quot; placeholder=&quot;닉네임&quot;&gt;--&gt;
        &lt;input type=&quot;text&quot; id=&quot;room&quot; placeholder=&quot;방 번호&quot;&gt;
        &lt;button onclick=&quot;manualConnect()&quot;&gt;Connect&lt;/button&gt;

    &lt;/div&gt;

    &lt;!-- 채팅 영역 (처음엔 숨김) --&gt;
    &lt;div id=&quot;chatWrapper&quot; class=&quot;hidden&quot;&gt;
        &lt;div id=&quot;chatArea&quot;&gt;&lt;/div&gt;
        &lt;div class=&quot;row&quot;&gt;
            &lt;input type=&quot;text&quot; id=&quot;msg&quot; placeholder=&quot;메시지&quot; onkeydown=&quot;if(event.key==='Enter'){sendMessage();}&quot;&gt;
            &lt;button onclick=&quot;sendMessage()&quot;&gt;Send&lt;/button&gt;
            &lt;button class=&quot;btn-disconnect&quot; onclick=&quot;disconnect()&quot;&gt;Disconnect&lt;/button&gt;
        &lt;/div&gt;
    &lt;/div&gt;
&lt;/div&gt;

&lt;script&gt;
    let ws = null;
    let roomId = &quot;&quot;;
    let nickname = &quot;&quot;;

    window.onload = function () {
        loadRoomList();
    };

    function loadRoomList() {
        fetch(&quot;/api/rooms&quot;)
            .then(response =&gt; response.json())
            .then(data =&gt; {
                const roomsDiv = document.getElementById(&quot;rooms&quot;);
                const roomListSection = document.getElementById(&quot;roomList&quot;);
                roomsDiv.innerHTML = &quot;&quot;;

                if (data.length === 0) {
                    roomListSection.classList.add(&quot;hidden&quot;);
                } else {
                    roomListSection.classList.remove(&quot;hidden&quot;);
                    data.forEach(room =&gt; {
                        const div = document.createElement(&quot;div&quot;);
                        div.className = &quot;room-item&quot;;
                        div.textContent = `방 번호: ${room.roomId}`;
                        div.onclick = () =&gt; enterRoom(room.roomId);
                        roomsDiv.appendChild(div);
                    });
                }
            });
    }

    function enterRoom(id, inputNick = null) {
        if (!inputNick) {
            const nick = prompt(&quot;닉네임을 입력하세요:&quot;);
            if (!nick) {
                alert(&quot;닉네임이 필요합니다.&quot;);
                return;
            }
            nickname = nick;
        } else {
            nickname = inputNick;
        }

        roomId = id;

      //방목록 데이터베이스에 저장하는 곳
      //방목록 데이터베이스에 저장하는 곳
      //방목록 데이터베이스에 저장하는 곳
      fetch(`/api/rooms/${roomId}`, { method: &quot;POST&quot; })


        ws = new WebSocket(&quot;/ws-chat&quot;);

        ws.onopen = function () {
            showSysMsg(`[${nickname}]님이 방 [${roomId}]에 입장했습니다.`);
            document.getElementById(&quot;chatWrapper&quot;).classList.remove(&quot;hidden&quot;);
            document.getElementById(&quot;roomList&quot;).classList.add(&quot;hidden&quot;);
            document.getElementById(&quot;nicknameRow&quot;).classList.add(&quot;hidden&quot;);
        };

        ws.onmessage = function (event) {
            const msg = JSON.parse(event.data);
            showMessage(msg.from, msg.message, msg.roomId);
        };

        ws.onclose = function () {
            showSysMsg(&quot;Disconnected&quot;);
            document.getElementById(&quot;chatWrapper&quot;).classList.add(&quot;hidden&quot;);
            document.getElementById(&quot;roomList&quot;).classList.remove(&quot;hidden&quot;);
            document.getElementById(&quot;nicknameRow&quot;).classList.remove(&quot;hidden&quot;);
            loadRoomList();
        };
    }

    function manualConnect() {
        const inputRoom = document.getElementById(&quot;room&quot;).value;
        if (!inputRoom) {
            alert(&quot;방 번호를 입력하세요.&quot;);
            return;
        }

        const inputNick = prompt(&quot;닉네임을 입력하세요:&quot;);
        if (!inputNick) {
            alert(&quot;닉네임이 필요합니다.&quot;);
            return;
        }

        nickname = inputNick;
        enterRoom(inputRoom, inputNick);
    }

    function disconnect() {
        if (ws) {
            ws.close();
            ws = null;
        }
    }

    function sendMessage() {
        const msg = document.getElementById(&quot;msg&quot;).value;
        if (!nickname || !msg || !roomId) {
            alert(&quot;모든 정보를 입력해주세요.&quot;);
            return;
        }

        ws.send(JSON.stringify({
            from: nickname,
            message: msg,
            roomId: roomId
        }));
        document.getElementById(&quot;msg&quot;).value = &quot;&quot;;
    }

    function showMessage(from, message, room) {
        const chatArea = document.getElementById(&quot;chatArea&quot;);
        chatArea.innerHTML += `&lt;div class=&quot;msgrow&quot;&gt;&lt;span class=&quot;from&quot;&gt;[${room}] ${from}:&lt;/span&gt; ${message}&lt;/div&gt;`;
        chatArea.scrollTop = chatArea.scrollHeight;
    }

    function showSysMsg(msg) {
        const chatArea = document.getElementById(&quot;chatArea&quot;);
        chatArea.innerHTML += `&lt;div class=&quot;sysmsg&quot;&gt;${msg}&lt;/div&gt;`;
        chatArea.scrollTop = chatArea.scrollHeight;
    }
&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;</code></pre>
<h2 id="6-웹에서-확인">6. 웹에서 확인</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/6ca71736-cbba-4f3c-b1d0-7e218594f6e5/image.png" /><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/b624b77b-7b89-46e4-b2e8-e739651a94d4/image.png" /></p>