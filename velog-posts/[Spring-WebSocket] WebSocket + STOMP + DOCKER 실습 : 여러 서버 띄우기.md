<h1 id="websocket--stomp--docker-실습--여러-서버-띄우기">[WebSocket + STOMP + DOCKER 실습 : 여러 서버 띄우기]</h1>
<blockquote>
<p>이전 WebSocket + STOMP 실습 파일을 가지고 Docker compose를 이용해 이미지와 컨테이너 생성 후 서버를 띄워보자. (로드밸런싱)</p>
</blockquote>
<h2 id="1-설정-파일-및-코드-수정">1. 설정 파일 및 코드 수정</h2>
<h3 id="1-실습1에서-만든-도커-컨테이너인-database-삭제">1. 실습1에서 만든 도커 컨테이너인 database 삭제</h3>
<h3 id="2-applicationproperties-수정">2. application.properties 수정</h3>
<pre><code class="language-properties">spring.application.name=backendProject

spring.datasource.url=jdbc:mysql://database:3306/backend?serverTimezone=Asia/Seoul&amp;characterEncoding=UTF-8&amp;rewriteBatchedStatements=true
spring.datasource.username=root    
spring.datasource.password=1234

spring.jpa.database-platform=org.hibernate.dialect.MySQL8Dialect
spring.jpa.hibernate.ddl-auto=update</code></pre>
<h3 id="3-docker-composeyml-수정">3. docker-compose.yml 수정</h3>
<pre><code class="language-yml">services:


  database:
    image: mysql:8
    container_name: database
    ports:
      - &quot;3307:3306&quot;
    environment:
      MYSQL_DATABASE: backend
      MYSQL_ROOT_PASSWORD: 1234
    volumes:
      - ./volumes/mysql-data:/var/lib/mysql #내가 지정한 경로
    healthcheck:
      test: [&quot;CMD&quot;, &quot;mysqladmin&quot;, &quot;ping&quot;, &quot;-h&quot;, &quot;localhost&quot;, '-u', 'root', '-p$$MYSQL_ROOT_PASSWORD']
      timeout: 20s
      retries: 10


  backend1:  # 서비스 이름은 컨테이너간 통신하기 위한 이름
    image: backend
    container_name: backend1
    environment:
      PROJECT_NAME: 백앤드 서버1
    depends_on:
      database:
        condition: service_healthy

  backend2:  # 서비스 이름은 컨테이너간 통신하기 위한 이름
    image: backend
    container_name: backend2
    environment:
      PROJECT_NAME: 백앤드 서버2
    depends_on:
      database:
        condition: service_healthy

  backend3:  # 서비스 이름은 컨테이너간 통신하기 위한 이름
    image: backend
    container_name: backend3
    environment:
      PROJECT_NAME: 백앤드 서버3
    depends_on:
      database:
        condition: service_healthy

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
<h3 id="4-nginxconf의-proxy_pass-매핑-주소-변경">4. nginx.conf의 proxy_pass 매핑 주소 변경</h3>
<pre><code class="language-conf">location /ws-chat {
            proxy_pass http://spring_backend;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection &quot;Upgrade&quot;;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }</code></pre>
<h3 id="5-chatcontrollerjava에-코드-추가">5. ChatController.java에 코드 추가</h3>
<pre><code class="language-java">    @Value(&quot;${PROJECT_NAME:web server}&quot;)
    private String instansName;

            template.convertAndSend(&quot;/topic/&quot;+message.getRoomId(), instansName + &quot; &quot; + message);</code></pre>
<h2 id="2-명령어-실행">2. 명령어 실행</h2>
<h3 id="1-bootjar-생성">1. bootjar 생성</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/f1802a8f-58f8-4a87-a90b-11c1255bd4b0/image.png" /></p>
<ul>
<li>Gradle -&gt; Tasks -&gt; build -&gt; bootJar</li>
</ul>
<h3 id="2-이미지-생성--docker-build--t-backend-">2. 이미지 생성 : docker build -t backend .</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/1a86e57b-b488-45e1-b18b-7fc64adf6abd/image.png" /></p>
<ul>
<li>dockerFile이 있는 경로에서 실행할 것.</li>
</ul>
<h3 id="3-컨테이너-생성--docker-compose-up--d">3. 컨테이너 생성 : docker compose up -d</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/925f4a9d-d667-4c3d-8ff2-5a1bbdd76c3d/image.png" /></p>
<ul>
<li>docker-compose.yml 파일이 있는 경로에서 실행할 것</li>
</ul>
<h2 id="3-웹에서-확인">3. 웹에서 확인</h2>
<h3 id="1-localhost-접속">1. localhost 접속</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/fa370105-2d12-491a-9d5c-0a115af45899/image.png" /></p>
<h3 id="2-설정한-서버백엔드1-백엔드2-백엔드3들로-나눠짐">2. 설정한 서버(백엔드1, 백엔드2, 백엔드3)들로 나눠짐</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/a0c738b7-8fa9-4b0b-a136-be253b0f1132/image.png" /></p>
<h3 id="3-같은-서버에-있으면-채팅과-귓속말-가능">3. 같은 서버에 있으면 채팅과 귓속말 가능</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/e29cdd2c-1f02-457c-a37c-1d15bcb4ca73/image.png" /><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/f4d99130-c6e6-42df-9afe-dfb9e2eefc47/image.png" /></p>