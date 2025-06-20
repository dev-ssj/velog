<blockquote>
<p>이전 포스트들과 이어집니다</p>
</blockquote>
<ol>
<li><a href="https://velog.io/@dev_ssj/SpringWebSocket-%EC%88%9C%EC%88%98-WebSocket-%EC%8B%A4%EC%8A%B5">[Spring/WebSocket] 순수 WebSocket으로 채팅방 만들기</a></li>
<li><a href="https://velog.io/@dev_ssj/SpringWebSocket-WebSocket-STOMP-%EC%8B%A4%EC%8A%B5">[Spring/WebSocket] WebSocket + STOMP으로 귓속말 가능한 채팅방 만들기</a></li>
<li><a href="https://velog.io/@dev_ssj/SpringWebSocket-WebSocket-STOMP-DOCKER-%EC%8B%A4%EC%8A%B5-%EC%97%AC%EB%9F%AC-%EC%84%9C%EB%B2%84-%EB%9D%84%EC%9A%B0%EA%B8%B0">[Spring/WebSocket] WebSocket + STOMP + DOCKER 실습 : 여러 서버 띄우기</a></li>
</ol>
<h1 id="redis-실습하기">[Redis 실습하기]</h1>
<blockquote>
<p><a href="https://velog.io/@dev_ssj/SpringWebSocket-WebSocket-STOMP-DOCKER-%EC%8B%A4%EC%8A%B5-%EC%97%AC%EB%9F%AC-%EC%84%9C%EB%B2%84-%EB%9D%84%EC%9A%B0%EA%B8%B0">[Spring/WebSocket] WebSocket + STOMP + DOCKER 실습 : 여러 서버 띄우기</a>에서 사용한 프로젝트에 redis를 붙여보자</p>
</blockquote>
<h2 id="1-redis-의존성-추가">1. redis 의존성 추가</h2>
<pre><code>implementation 'org.springframework.boot:spring-boot-starter-data-redis’</code></pre><h2 id="2-stompwebsocket-하위에-redis-패키지-추가-후-코드-작성">2. stompwebsocket 하위에 redis 패키지 추가 후 코드 작성</h2>
<h3 id="2-1-redispublisherjava">2-1. RedisPublisher.java</h3>
<pre><code class="language-java">package com.example.backendproject.stompwebsocket.redis;


import lombok.RequiredArgsConstructor;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.stereotype.Component;

@RequiredArgsConstructor
@Component
public class RedisPublisher {

    /** 메세지를 발행하는 클래스 **/

    private final StringRedisTemplate stringRedisTemplate;

    ///  stomp -&gt; pub -&gt; sub -&gt; stomp
    public void publish(String channel,String message){

        stringRedisTemplate.convertAndSend(channel,message);
    }

}
</code></pre>
<h3 id="2-2-redissubscriberjava">2-2. RedisSubscriber.java</h3>
<pre><code class="language-java">package com.example.backendproject.stompwebsocket.redis;
import com.example.backendproject.stompwebsocket.dto.ChatMessage;
import com.fasterxml.jackson.databind.ObjectMapper;
import lombok.RequiredArgsConstructor;
import org.springframework.data.redis.connection.Message;
import org.springframework.data.redis.connection.MessageListener;
import org.springframework.messaging.simp.SimpMessagingTemplate;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class RedisSubscriber implements MessageListener{


    private final SimpMessagingTemplate simpMessagingTemplate;
    private ObjectMapper objectMapper = new ObjectMapper();


    @Override
    public void onMessage(Message message, byte[] pattern) {

        try {
            String msgBody = new String(message.getBody());
            ChatMessage chatMessage = objectMapper.readValue(msgBody, ChatMessage.class);


            if (chatMessage.getTo() != null &amp;&amp; !chatMessage.getTo().isEmpty()) {
                // 귓속말
                simpMessagingTemplate.convertAndSendToUser(chatMessage.getTo(), &quot;/queue/private&quot;, chatMessage);
            } else {
                // 일반 메시지
                simpMessagingTemplate.convertAndSend(&quot;/topic/room.&quot; + chatMessage.getRoomId(), chatMessage);


            }
        }
        catch (Exception e) {

        }

    }

}</code></pre>
<h3 id="2-3-redisconfigjava">2-3. redisConfig.java</h3>
<pre><code class="language-java">package com.example.backendproject.stompwebsocket.redis;

import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.listener.PatternTopic;
import org.springframework.data.redis.listener.RedisMessageListenerContainer;
import org.springframework.data.redis.listener.adapter.MessageListenerAdapter;

@Configuration
@RequiredArgsConstructor
public class RedisConfig {


    private final RedisSubscriber redisSubscriber;

    @Bean
    public RedisMessageListenerContainer redisContainer(RedisConnectionFactory redisConnectionFactory) {
        RedisMessageListenerContainer Container = new RedisMessageListenerContainer();
        Container.setConnectionFactory(redisConnectionFactory);

        Container.addMessageListener(new MessageListenerAdapter(redisSubscriber),new PatternTopic(&quot;room.*&quot;));
        Container.addMessageListener(new MessageListenerAdapter(redisSubscriber), new PatternTopic(&quot;private.*&quot;)); //귓속말

        return Container;

    }
}</code></pre>
<h2 id="3-설정-파일-및-기존-코드-수정">3. 설정 파일 및 기존 코드 수정</h2>
<h3 id="3-1-env-파일-추가">3-1. .env 파일 추가</h3>
<ul>
<li>backendProject 폴더의 상위 폴더인 backend5에 .env파일 추가<pre><code>DB_SERVER=database
DB_PORT=3306
DB_USER=root
DB_PASS=1234
</code></pre></li>
</ul>
<p>REDIS_HOST=redis</p>
<pre><code>
### 3-2. application.properties 변경
```properties
db.server=${DB_SERVER:localhost}
db.port=${DB_PORT:3307}
db.username=${DB_USER:root}
db.password=${DB_PASS:1234}
REDIS.HOST=${REDIS_HOST:localhost}

spring.data.redis.host=${REDIS.HOST}
spring.data.redis.port=6379

spring.datasource.url=jdbc:mysql://${db.server}:${db.port}/backendDB?serverTimezone=Asia/Seoul&amp;characterEncoding=UTF-8&amp;rewriteBatchedStatements=true
spring.datasource.username=${db.username}
spring.datasource.password=${db.password}</code></pre><h3 id="3-3-docker-composeyml-변경">3-3. docker-compose.yml 변경</h3>
<pre><code class="language-yaml">services:

 redis:
    image: redis
    container_name: redis
    ports:
      - &quot;6379:6379&quot;

  database:
    ...
    ports:
      - &quot;3307:3306&quot;
    environment:
      MYSQL_DATABASE: backendDB
      MYSQL_ROOT_PASSWORD: ${DB_PASS}
    ...

  backend1:  # 서비스 이름은 컨테이너간 통신하기 위한 이름
    image: backend
    container_name: backend1
    environment:
      PROJECT_NAME: 백앤드 서버1
      DB_SERVER: ${DB_SERVER}
      DB_PORT: ${DB_PORT}
      DB_USER: ${DB_USER}
      DB_PASS: ${DB_PASS}
      REDIS_HOST: ${REDIS_HOST}
    depends_on:
      - database
      - redis

  backend2:  # 서비스 이름은 컨테이너간 통신하기 위한 이름
    image: backend
    container_name: backend2
    environment:
      PROJECT_NAME: 백앤드 서버2
      DB_SERVER: ${DB_SERVER}
      DB_PORT: ${DB_PORT}
      DB_USER: ${DB_USER}
      DB_PASS: ${DB_PASS}
      REDIS_HOST: ${REDIS_HOST}
    depends_on:
      - database
      - redis

  backend3:  # 서비스 이름은 컨테이너간 통신하기 위한 이름
    image: backend
    container_name: backend3
    environment:
      PROJECT_NAME: 백앤드 서버3
      DB_SERVER: ${DB_SERVER}
      DB_PORT: ${DB_PORT}
      DB_USER: ${DB_USER}
      DB_PASS: ${DB_PASS}
      REDIS_HOST: ${REDIS_HOST}
    depends_on:
      - database
      - redis


  nginx:
    image: nginx:1.25
    container_name: nginx
    ports:
      - &quot;80:80&quot;
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - backend1
      - backend2
      - backend3</code></pre>
<h3 id="3-4-chatcontrollerjava-변경">3-4. chatController.java 변경</h3>
<pre><code class="language-java">//STOMP는 이게 끝.
@Controller
@RequiredArgsConstructor
public class ChatController {

    //서버가 클라이언트에게 수동으로 메세지를 보낼 수 있도록 하는 클래스
    private final SimpMessagingTemplate template;

    //동적으로 방 생성 가능
    @Value(&quot;${PROJECT_NAME:web Server}&quot;)
    private String instansName;

    //redis 관련 변수
    private final RedisPublisher redisPublisher;
    private ObjectMapper objectMapper  = new ObjectMapper();

    @MessageMapping(&quot;/chat.sendMessage&quot;)
    public void sendmessage(ChatMessage message) throws JsonProcessingException {

        message.setMessage(instansName+&quot; &quot;+message.getMessage());

        String channel = null;
        String msg = null;

        if (message.getTo() != null &amp;&amp; !message.getTo().isEmpty()) {
            // 귓속말
            //내 아이디로 귓속말경로를 활성화 함
            channel = &quot;private.&quot;+message.getRoomId();
            msg = objectMapper.writeValueAsString(message);

        } else {
            // 일반 메시지
            channel = &quot;room.&quot;+message.getRoomId();
            msg = objectMapper.writeValueAsString(message);
        }
        redisPublisher.publish(channel,msg);
    }
}
</code></pre>
<h2 id="4-웹에서-확인">4. 웹에서 확인</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/e1eee903-dd49-4647-ab4d-57d163096f54/image.png" /><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/26f6e412-7039-4f2c-86dd-b0fd549f8489/image.png" /></p>
<blockquote>
<p>redis 설정 후에는 서버가 달라도 채팅 가능!</p>
</blockquote>