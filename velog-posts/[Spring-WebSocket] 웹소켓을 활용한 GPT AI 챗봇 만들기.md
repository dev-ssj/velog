<blockquote>
<p>이전 포스트들과 이어집니다</p>
</blockquote>
<ol>
<li><a href="https://velog.io/@dev_ssj/SpringWebSocket-%EC%88%9C%EC%88%98-WebSocket-%EC%8B%A4%EC%8A%B5">[Spring/WebSocket] 순수 WebSocket으로 채팅방 만들기</a></li>
<li><a href="https://velog.io/@dev_ssj/SpringWebSocket-WebSocket-STOMP-%EC%8B%A4%EC%8A%B5">[Spring/WebSocket] WebSocket + STOMP으로 귓속말 가능한 채팅방 만들기</a></li>
<li><a href="https://velog.io/@dev_ssj/SpringWebSocket-WebSocket-STOMP-DOCKER-%EC%8B%A4%EC%8A%B5-%EC%97%AC%EB%9F%AC-%EC%84%9C%EB%B2%84-%EB%9D%84%EC%9A%B0%EA%B8%B0">[Spring/WebSocket] WebSocket + STOMP + DOCKER 실습 : 여러 서버 띄우기</a></li>
<li><a href="https://velog.io/@dev_ssj/SpringWebSocket-%EC%9B%B9%EC%86%8C%EC%BC%93%EC%9D%84-%ED%99%9C%EC%9A%A9%ED%95%9C-GPT-AI-%EC%B1%97%EB%B4%87-%EB%A7%8C%EB%93%A4%EA%B8%B0">[Spring/WebSocket] 웹소켓을 활용한 GPT AI 챗봇 만들기</a></li>
</ol>
<h1 id="💡웹소켓을-활용한-gpt-ai-챗봇-만들기">💡웹소켓을 활용한 GPT AI 챗봇 만들기</h1>
<h2 id="🔍챗봇-구조">🔍챗봇 구조</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/f80da808-8603-48f5-806f-2b1fdf016ca6/image.png" /></p>
<blockquote>
<p><strong>❓브라우저와 GPT 사이에 웹소켓이 중간에 끼는 이유</strong></p>
</blockquote>
<ul>
<li><strong>브라우저에서 바로 GPT에 직접 보내게 되면 클라이언트에 API 키가 노출</strong>되며, 로깅과 제어가 불가하다.</li>
<li>중간에 웹소켓이 끼게 되면 <strong>API 키 노출을 방지할 수 있고, 실시간 응답이 가능</strong>하다.</li>
</ul>
<h2 id="✅지피티-api-키-테스트">✅지피티 API 키 테스트</h2>
<h3 id="1-httpsplatformopenaicom에서-api-키-생성">1. <a href="https://platform.openai.com/%EC%97%90%EC%84%9C">https://platform.openai.com/에서</a> API 키 생성</h3>
<h3 id="2-테스트-형식-확인">2. 테스트 형식 확인</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/7f82ef84-426f-428e-bc10-a399bad450f6/image.png" /></p>
<pre><code class="language-shell">curl https://api.openai.com/v1/responses \
    -H &quot;Content-Type: application/json&quot; \
    -H &quot;Authorization: Bearer $OPENAI_API_KEY&quot; \
    -d '{
        &quot;model&quot;: &quot;gpt-4.1&quot;,
        &quot;input&quot;: &quot;Write a one-sentence bedtime story about a unicorn.&quot;
    }'</code></pre>
<ul>
<li>$OPENAI_API_KEY 에 본인의 api 키를 넣어주면 된다.</li>
</ul>
<h3 id="3-postman에서-작성">3. PostMan에서 작성</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/40c9bf84-f0d8-40ce-87d5-df3055817a11/image.png" /></p>
<ul>
<li>Authorization은 위에서 발급받은 api키를 넣어주면 된다. api 키 앞에 Bearer를 붙여주어야된다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/6afbd7ee-b5c9-4900-b548-d8500b5f0128/image.png" /></li>
<li>Body에는 gpt 모델과 질문을 써주면 된다.</li>
</ul>
<h3 id="4-결과-확인">4. 결과 확인</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/2f681b3b-d82d-4a4c-8fcb-d92474de0cd4/image.png" /></p>
<h2 id="✅localhost8080으로-ai봇-만들기">✅localhost:8080으로 AI봇 만들기</h2>
<h3 id="1-stompwebsocket-패키지-하위에-gpt-패키지-추가-후-gptservicejava-작성">1. stompwebsocket 패키지 하위에 gpt 패키지 추가 후 GPTService.java 작성</h3>
<pre><code class="language-java">@Service
public class GPTService {
    //json 문자열 &lt;-&gt; 자바객체, json 객체
    private final ObjectMapper mapper = new ObjectMapper();

    //@Value(&quot;${openai.api-key}&quot;)
    private String openaiApiKey;

    public String getMessage(String message) throws Exception {
        try {
            //API 호출을 위한 본문 작성
            Map&lt;String, Object&gt; requestBody = new HashMap&lt;&gt;();
            requestBody.put(&quot;model&quot;, &quot;gpt-4o&quot;);
            requestBody.put(&quot;input&quot;, message);

            //http 요청 작성
            HttpRequest request = HttpRequest.newBuilder()
                    .uri(URI.create(&quot;https://api.openai.com/v1/responses&quot;))
                    .header(&quot;Authorization&quot;, &quot;Bearer {API키}&quot;)
                    .header(&quot;Content-Type&quot;, &quot;application/json&quot;)
                    .POST(HttpRequest.BodyPublishers.ofString(mapper.writeValueAsString(requestBody)))  //본문 삽입
                    .build();

            //요청 전송 및 응답
            HttpClient client = HttpClient.newHttpClient();
            HttpResponse&lt;String&gt; response = client.send(request, HttpResponse.BodyHandlers.ofString());

            //응답을 JSON으로 파싱
            JsonNode jsonNode = mapper.readTree(response.body());
            System.out.println(&quot;get 응답 : &quot; + jsonNode);

            //메시지 부분만 추출하여 반환(응답형태가 Json인데 json문자열 복잡함 -&gt; 거기서 지피티의 대답만 추출)
            String gptMessageResponse = jsonNode.get(&quot;output&quot;).get(0).get(&quot;content&quot;).get(0).get(&quot;text&quot;).asText();
            return gptMessageResponse;

            //응답이 오지않으면 예외처리
        } catch (Exception e) {
            return &quot;❗문제가 발생했습니다. 다시 시도해주세요.&quot;;
        }
    }
}</code></pre>
<h3 id="2-chatcontroller-수정">2. ChatController 수정</h3>
<pre><code class="language-java">//환경 변수를 받아서 화면에 출력
    @Value(&quot;${PROJECT_NAME:web Server}&quot;)
    private String instansName;
    private final RedisPublisher redisPublisher;
    private ObjectMapper objectMapper  = new ObjectMapper();

    private final GPTService gptService;

     //단일 브로드 캐스트(방 동적 생성 불가)
    @MessageMapping(&quot;/gpt&quot;)
    public void sendMessageGPT(ChatMessage message) throws Exception{

        template.convertAndSend(&quot;/topic/gpt&quot;,message);//내가 보낸 메시지 출력

        //사용자가 보낸 메시지를 받음. gpt 목적지 반환
        String getResponse = gptService.getMessage(message.getMessage());

        ChatMessage chatMessage = new ChatMessage(&quot;난 GPT &quot;, getResponse);

        template.convertAndSend(&quot;/topic/gpt&quot;, chatMessage);
  }</code></pre>
<h3 id="3-chatmessage-생성자-추가">3. ChatMessage 생성자 추가</h3>
<pre><code class="language-java">  public ChatMessage(String message, String from) {
        this.from =from;
        this.message = message;
    }</code></pre>
<h3 id="4-websocketconfig-수정">4. WebSocketConfig 수정</h3>
<pre><code class="language-java"> public void registerStompEndpoints(StompEndpointRegistry registry) {
        //채팅방 엔드포인트
        registry.addEndpoint(&quot;/ws-chat&quot;)
                .setHandshakeHandler(new CustomHandshakeHandler()) //귓속말 가능하게 해줌
                .setAllowedOriginPatterns(&quot;*&quot;);

        //지피티 엔드포인트
        registry.addEndpoint(&quot;/ws-gpt&quot;)
                .setAllowedOriginPatterns(&quot;*&quot;);
    }</code></pre>
<h3 id="5-indexhtml-gpthtml-추가">5. index.html, gpt.html 추가</h3>
<h3 id="6-도커-컨테이너에서-redis-database-키기">6. 도커 컨테이너에서 redis, database 키기</h3>
<ul>
<li>docer compose up -d database reids</li>
</ul>
<h3 id="7-localhost8080-에서-확인">7. localhost:8080 에서 확인</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/70444f66-8487-4d34-a4fa-4ef9e9b16f52/image.png" />
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/7d681370-754a-41a1-ab05-6bdd38d8acb8/image.png" /></p>
<h2 id="✅localhost로-ai봇-만들기">✅localhost로 AI봇 만들기</h2>
<h3 id="nginxconf-수정">nginx.conf 수정</h3>
<pre><code>worker_processes 1;

events {
    worker_connections 1024;
}

http {
    upstream spring_backend {
        server backend1:8080;
        server backend2:8080;
        server backend3:8080;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://spring_backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

        location /ws-chat {
            proxy_pass http://spring_backend;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection &quot;Upgrade&quot;;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

        location /ws-gpt {
                    proxy_pass http://spring_backend;
                    proxy_http_version 1.1;
                    proxy_set_header Upgrade $http_upgrade;
                    proxy_set_header Connection &quot;Upgrade&quot;;
                    proxy_set_header Host $host;
                    proxy_set_header X-Real-IP $remote_addr;
                    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                }

    }
}</code></pre><h3 id="2-bootjar---docker-build--t-backend----docker-compose-up--d">2. bootjar -&gt; docker build -t backend . -&gt; docker compose up -d</h3>
<h3 id="3-localhost에서-확인">3. localhost에서 확인</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/367ad89f-a1e8-44d9-96f8-d55b1c46983f/image.png" /></p>