<h1 id="1-귓속말--stomp">1. [귓속말 + STOMP]</h1>
<h2 id="1-프로젝트-하위에-stompwebsocket패키지-생성-및-파일-작성">1. 프로젝트 하위에 stompwebsocket패키지 생성 및 파일 작성</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/c3ca004a-54dd-4fb0-b69a-7748c2a19567/image.png" /></p>
<h3 id="1-1-주의사항">1-1. 주의사항</h3>
<p>purewebsocket.config.WebSocketConfig의 @EnableWebSocket, @Configu은 주석처리 
→@EnableWebSocketMessageBroker과 중복되면 에러발생(STOMP)
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/78fac692-2544-4da8-94d7-6c617b782d53/image.png" /></p>
<h3 id="1-2-websocketconfigjava">1-2. websocketConfig.java</h3>
<pre><code class="language-java">package com.example.backendproject.stompwebsocket.config;

import com.example.backendproject.stompwebsocket.handler.CustomHandshakeHandler;
import org.springframework.context.annotation.Configuration;
import org.springframework.messaging.simp.config.MessageBrokerRegistry;
import org.springframework.web.socket.config.annotation.EnableWebSocketMessageBroker;
import org.springframework.web.socket.config.annotation.StompEndpointRegistry;
import org.springframework.web.socket.config.annotation.WebSocketMessageBrokerConfigurer;

@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {
    @Override
    public void registerStompEndpoints(StompEndpointRegistry registry) {
        registry.addEndpoint(&quot;/ws-chat&quot;)
                .setHandshakeHandler(new CustomHandshakeHandler()) //귓속말 가능하게 해줌
                .setAllowedOriginPatterns(&quot;*&quot;);
    }

    @Override
    public void configureMessageBroker(MessageBrokerRegistry registry) {
        //Prefix &lt;- 메세지의 목적지를 구분하기 위한 접두고

        /** 구독용 Profix **/

        // /topic : 일반 채팅 받을 접두어
        // /queue : 귓속말 받을 접두어

        //구독용 경로 서버 -&gt; 클라이언트(메시지를 분배한다)
        registry.enableSimpleBroker(&quot;/topic&quot;, &quot;/queue&quot;);
        ```
        //전송용 경로 클라이언트 -&gt; 서버 (메시지가 들어온다)
        registry.setApplicationDestinationPrefixes(&quot;/app&quot;);

        // /user 특정 사용자에게 메시지를 보낼 접두어
        /** 서버가 특정 사용자에게 메시지를 보낼 떄, 클라이언트가 구독할 경로 접두어 **/
        registry.setUserDestinationPrefix(&quot;/user&quot;); //서버 -&gt; 특정사용자
    }


}</code></pre>
<h3 id="1-3chatcontrollerjava">1-3.ChatController.java</h3>
<pre><code class="language-java">package com.example.backendproject.stompwebsocket.controller;

import com.example.backendproject.stompwebsocket.dto.ChatMessage;
import lombok.RequiredArgsConstructor;
import org.springframework.messaging.handler.annotation.MessageMapping;
import org.springframework.messaging.simp.SimpMessagingTemplate;
import org.springframework.stereotype.Controller;

//STOMP는 이게 끝.
@Controller
@RequiredArgsConstructor
public class ChatController {

    private final SimpMessagingTemplate template;

    //동적으로 방 생성 가능
    @MessageMapping(&quot;/chat.sendMessage&quot;)
    public void sendmessage(ChatMessage message){
        if(message.getTo() != null &amp;&amp; !message.getTo().isEmpty()){
            //귓속말
            //내 아이디로 귓속말 경로를 활성화 함
            template.convertAndSendToUser(message.getTo(), &quot;/queue/private&quot;,message);
        }else {
            //일반 메시지
            //message에서 roomId를 추출해서 해당 roomId를 구독하고 있는 클라이언트에게 메시지를 전달
            template.convertAndSend(&quot;/topic/&quot;+message.getRoomId(),message);
        }
    }
}</code></pre>
<h3 id="1-4-chatmessagejava">1-4. ChatMessage.java</h3>
<pre><code class="language-java">package com.example.backendproject.stompwebsocket.dto;


import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class ChatMessage {
    private String message;
    private String from;

    private String to;  //귓속말을 받을 사람
    private String roomId;  //방 id
}</code></pre>
<h3 id="1-5-customhandshakehandlerjava">1-5. CustomHandshakeHandler.java</h3>
<pre><code class="language-java">package com.example.backendproject.stompwebsocket.handler;

import org.springframework.http.server.ServerHttpRequest;
import org.springframework.web.socket.WebSocketHandler;
import org.springframework.web.socket.server.support.DefaultHandshakeHandler;

import java.security.Principal;
import java.util.Map;

//연결된 요청 url에서 사용자를 식별
public class CustomHandshakeHandler extends DefaultHandshakeHandler {
    @Override
    protected Principal determineUser(ServerHttpRequest request, WebSocketHandler wsHandler, Map&lt;String, Object&gt; attributes) {

        String nickname = getNickname(request.getURI().getQuery());
        return new StompPricipal(nickname);
    }

    //요청이 들어오면 닉네임을 추출해서 닉네임이 없으면 닉네임 없음 출력, 있으면 사용자 추출하는 핸들러
    private String getNickname(String query){
        if(query == null || !query.contains(&quot;nickname=&quot;)){
            System.out.println(&quot;겟닉네임작동&quot;);
            return &quot;닉네임 없음&quot;;
        }
        else{
            return query.split(&quot;nickname=&quot;)[1];
        }
    }
}</code></pre>
<h3 id="1-6-stomppricipal">1-6. StompPricipal</h3>
<pre><code class="language-java">package com.example.backendproject.stompwebsocket.handler;

import java.security.Principal;

public class StompPricipal implements Principal {

    private final String name;

    public StompPricipal(String name) {
        this.name = name;
    }

    @Override
    public String getName() {
        return name;
    }
}</code></pre>
<h3 id="1-7-stompcaht2html">1-7. stompcaht2.html</h3>
<pre><code class="language-java">&lt;!DOCTYPE html&gt;
&lt;html lang=&quot;ko&quot;&gt;
&lt;head&gt;
    &lt;meta charset=&quot;UTF-8&quot;&gt;
    &lt;title&gt;WebSocket STOMP Chat&lt;/title&gt;
    &lt;style&gt;
        body { font-family: 'Segoe UI', sans-serif; background: #f7f8fa; }
        .container {
            width: 400px; margin: 60px auto; background: #fff; padding: 32px 30px;
            border-radius: 14px; box-shadow: 0 2px 8px rgba(0,0,0,0.07);
        }
        h2 { text-align: center; color: #2c3e50; margin-bottom: 20px;}
        #chatArea {
            width: 100%; height: 250px; border: 1px solid #aaa;
            margin-bottom: 18px; overflow-y: auto; padding: 10px 7px; border-radius: 8px;
            background: #fafdff; font-size: 15px;
        }
        .row { display: flex; gap: 10px; align-items: center; margin-bottom: 12px; }
        input[type=&quot;text&quot;] {
            box-sizing: border-box; border: 1px solid #ccc; border-radius: 6px;
            font-size: 15px; padding: 9px; outline: none; background: #f9fafd;
            transition: border 0.2s;
        }
        input[type=&quot;text&quot;]:focus { border-color: #4078c0; background: #fff; }
        #user, #room { width: 110px; }
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
        #roomList { margin-bottom: 20px; }
        .room-item {
            padding: 6px 10px; border: 1px solid #ccc; border-radius: 6px; margin-bottom: 5px;
            cursor: pointer;
        }
        .room-item:hover { background: #e8f0ff; }
    &lt;/style&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;div class=&quot;container&quot;&gt;
    &lt;h2&gt;Spring WebSocket + STOMP Chat&lt;/h2&gt;

    &lt;!-- 방 목록 --&gt;
    &lt;div id=&quot;roomList&quot;&gt;
        &lt;strong&gt;방 목록:&lt;/strong&gt;
        &lt;div id=&quot;rooms&quot;&gt;&lt;/div&gt;
    &lt;/div&gt;

    &lt;!-- 입장 영역 --&gt;
    &lt;div class=&quot;row&quot; id=&quot;enterRow&quot;&gt;
        &lt;input type=&quot;text&quot; id=&quot;user&quot; placeholder=&quot;닉네임&quot;&gt;
        &lt;input type=&quot;text&quot; id=&quot;room&quot; placeholder=&quot;방 번호&quot;&gt;
        &lt;button onclick=&quot;connect()&quot;&gt;Connect&lt;/button&gt;
    &lt;/div&gt;

    &lt;!-- 귓속말 대상 --&gt;
    &lt;div class=&quot;row hidden&quot; id=&quot;whisperRow&quot;&gt;
        &lt;input type=&quot;text&quot; id=&quot;whisperTo&quot; placeholder=&quot;귓속말 대상 (닉네임)&quot;&gt;
    &lt;/div&gt;

    &lt;!-- 채팅 영역 (입장 후에만 표시) --&gt;
    &lt;div id=&quot;chatWrapper&quot; class=&quot;hidden&quot;&gt;
        &lt;div id=&quot;chatArea&quot;&gt;&lt;/div&gt;
        &lt;div class=&quot;row&quot;&gt;
            &lt;input type=&quot;text&quot; id=&quot;msg&quot; placeholder=&quot;메시지&quot;&gt;
            &lt;button onclick=&quot;sendMessage()&quot;&gt;Send&lt;/button&gt;
            &lt;button class=&quot;btn-disconnect&quot; onclick=&quot;disconnect()&quot;&gt;Disconnect&lt;/button&gt;
        &lt;/div&gt;
    &lt;/div&gt;
&lt;/div&gt;

&lt;script src=&quot;https://cdn.jsdelivr.net/npm/stompjs@2.3.3/lib/stomp.min.js&quot;&gt;&lt;/script&gt;
&lt;script&gt;
    let stompClient = null;
    let nickname = &quot;&quot;;
    let roomId = &quot;&quot;;

    window.onload = function () {
        loadRoomList();
    };

    function loadRoomList() {

        fetch(&quot;/api/rooms&quot;)
            .then(response =&gt; response.json())
            .then(data =&gt; {
                const roomsDiv = document.getElementById(&quot;rooms&quot;);
                const roomListDiv = document.getElementById(&quot;roomList&quot;);
                roomsDiv.innerHTML = &quot;&quot;;
                if (data.length === 0) {
                    roomListDiv.classList.add(&quot;hidden&quot;);
                } else {
                    roomListDiv.classList.remove(&quot;hidden&quot;);
                    data.forEach(room =&gt; {
                        const div = document.createElement(&quot;div&quot;);
                        div.className = &quot;room-item&quot;;
                        div.textContent = `방 번호: ${room.roomId}`;
                        div.onclick = () =&gt; {
                            const nick = prompt(&quot;닉네임을 입력하세요:&quot;);
                            if (!nick) return;
                            document.getElementById(&quot;user&quot;).value = nick;
                            document.getElementById(&quot;room&quot;).value = room.roomId;
                            connect();
                        };
                        roomsDiv.appendChild(div);
                    });
                }
            });
    }

    function connect() {
        nickname = document.getElementById(&quot;user&quot;).value;
        roomId = document.getElementById(&quot;room&quot;).value;

        if (!nickname || !roomId) {
            alert(&quot;닉네임과 방 번호를 입력하세요!&quot;);
            return;
        }

        //방생성기능
        //방생성기능
       fetch(`/api/rooms/${roomId}`, { method: &quot;POST&quot; })

      //  const socket = new WebSocket('/ws-chat');
        const socket = new WebSocket('/ws-chat?nickname=' + nickname);
        stompClient = Stomp.over(socket);

        stompClient.connect({}, function (frame) {
            showSysMsg(`[${nickname}]님이 방 [${roomId}]에 입장했습니다.`);
            document.getElementById(&quot;chatWrapper&quot;).classList.remove(&quot;hidden&quot;);
            document.getElementById(&quot;enterRow&quot;).classList.add(&quot;hidden&quot;);
            document.getElementById(&quot;roomList&quot;).classList.add(&quot;hidden&quot;);

           document.getElementById(&quot;whisperRow&quot;).classList.remove(&quot;hidden&quot;); // ✅ 여기!  닉네임 작성


            stompClient.subscribe(`/topic/${roomId}`, function (chat) {
                const message = JSON.parse(chat.body);
                showMessage(message.from, message.message);
            });


            //귓속말
            stompClient.subscribe('/user/queue/private', function (message) {
                const msg = JSON.parse(message.body);
                alert(&quot;💬 귓속말: &quot; + msg.from + &quot; - &quot; + msg.message);
            });

        });


    }

    function disconnect() {
        if (stompClient !== null) {
            stompClient.disconnect();
        }
        showSysMsg('Disconnected');
        document.getElementById(&quot;chatWrapper&quot;).classList.add(&quot;hidden&quot;);
        document.getElementById(&quot;enterRow&quot;).classList.remove(&quot;hidden&quot;);
        document.getElementById(&quot;roomList&quot;).classList.remove(&quot;hidden&quot;);
        document.getElementById(&quot;whisperRow&quot;).classList.add(&quot;hidden&quot;); // ✅ 추가
        loadRoomList();
    }

    function sendMessage() {
        const msg = document.getElementById(&quot;msg&quot;).value;

        const toUser = document.getElementById(&quot;whisperTo&quot;).value.trim();

        if (!nickname || !msg || !roomId) {
            alert(&quot;모든 정보를 입력해주세요!&quot;);
            return;
        }

        const payload = {
            from: nickname,
            message: msg,
            roomId: roomId
        };

        if (toUser) {
            payload.to = toUser; // 귓속말 대상이 있으면 to 추가
        }

        stompClient.send(&quot;/app/chat.sendMessage&quot;, {}, JSON.stringify(payload));
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

    document.getElementById('msg').addEventListener('keydown', function(e) {
        if (e.key === 'Enter') sendMessage();
    });
&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;</code></pre>
<h2 id="2-htmlcontroller에서-stompchat2html로-변경">2. htmlController에서 stompchat2.html로 변경</h2>
<pre><code class="language-java">@Controller
public class HtmlController {
    @GetMapping(&quot;/&quot;)
    public String index(){
        return &quot;redirect:/stompchat2.html&quot;;
    }
}</code></pre>
<h2 id="3-결과-확인">3. 결과 확인</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/0e916adb-906b-4913-b403-434da571911f/image.png" /><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/1bda8a03-57f9-40fb-8cec-aeec73321ddd/image.png" /></p>