<h1 id="💡docker">💡Docker</h1>
<blockquote>
<p><strong>컨테이너</strong>라는 기술을 사용해서 애플리케이션을 <strong>언제 어디서나 같은 환경</strong>에서 배포하고 실행할 수 있게 해주는 도구</p>
</blockquote>
<hr />
<h2 id="✅도커의-주요-구성-요소">✅도커의 주요 구성 요소</h2>
<ol>
<li><strong>이미지(Image)</strong> : 컨테이너를 생성하기 위한 <strong>실행 파일의 템플릿</strong>. 애플리케이션 실행에 필요한 코드, 라이브러리, 환경설정 등을 포함한다.</li>
<li><strong>컨테이너(Container)</strong> : 이미지를 기반으로 실행되는 <strong>독립적인 실행환경</strong></li>
<li><strong>레지스트리(Registry)</strong> : <strong>Docker 이미지를 저장하고 배포하는 저장소</strong>. 공식 이미지 저장소와 개인/회사 전용 프라이빗 레지스트리가 있다.
 →Docker Hub : 전세계 사람들이 만든 Docker 이미지를 공유하고 내려받을 수 있는 공식 이미지 저장소</li>
<li><strong>네트워크(Network)</strong> : 컨테이너들은 각자 격리되어 있기 때문에 도커 네트워크가 없으면 서로 데이터를 주고받지 못함. <strong>컨테이너 간 통신</strong>을 위해 브리지, 호스트, 오버레이 네트워크 등 설정 가능</li>
<li><strong>볼륨(Volume)</strong> : 컨테이너는 일시적 환경임 -&gt; 즉, 컨테이너가 삭제되면 컨테이너 안에 있는 데이터들도 삭제된다. 이러한 문제점을 보완해주는게 볼륨이다. <strong>볼륨을 설정하면 컨테이너를 삭제하고 다시 생성해도 데이터가 그대로 남아있다</strong>. 그래서 데이터를 다루는 컨테이너는 볼륨을 설정해주는게 기본임.</li>
</ol>
<hr />
<h1 id="💡도커-명령어-실습">💡도커 명령어 실습</h1>
<h2 id="1️⃣docker-run--컨테이너-생성--실행">1️⃣docker run : 컨테이너 생성 &amp; 실행</h2>
<h3 id="✅명령어">✅명령어</h3>
<pre><code class="language-bash">docker run --name server -d -p 8080:80 nginx</code></pre>
<h3 id="✅세부-설명">✅세부 설명</h3>
<ol>
<li><strong>--name [컨테이너이름]</strong> : 생성할 컨테이너 이름 지정</li>
<li><strong>-d</strong> : detached mode. 터미널을 점유하지 않고 백그라운드에서 실행. 기본적으로 적어야함</li>
<li><strong>-p [호스트 포트]:[컨테이너 포트]</strong> : 포트포워딩. 호스트의 포트를 컨테이너 내부 포트에 연결해 외부에서 접근할 수 있게 해주는 옵션.
위 예제에서는 -p 8080:80으로 명시해뒀는데, 이 뜻은 Nginx의 기본 포트는 80번이고, 브라우저에서 localhost:8080으로 들어가게 되면, 도커가 그 요청을 컨테이너의 80번 포트로 전달해준다는 뜻이다.</li>
<li><strong>nginx</strong> : 공식 nginx Docker 이미지를 사용하겠다 명시</li>
</ol>
<p>➡️<strong>이름이 server인 nginx 웹 서버 컨테이너를 만들고, 호스트의 8080 포트를 Nginx의 80포트로 연결하여 백그라운드로 실행하는 명령어</strong></p>
<h3 id="📍실행결과">📍실행결과</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/5166b288-f399-421a-8b07-f75c20c70e3c/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/a65407bd-d5e2-4318-8a3c-e0aeb607ad10/image.png" /></p>
<blockquote>
<h3 id="🔍nginx란">🔍Nginx란?</h3>
<p>Nginx는 가볍고 빠른 성능을 자랑하는 <strong>웹 서버 소프트웨어</strong>이다. Nginx는 주로 이런 용도로 사용된다.</p>
</blockquote>
<ol>
<li><strong>정적 파일 제공</strong> : HTML, CSS, JS, 이미지 등 정적 리소스를 빠르게 전달</li>
<li><strong>리버스 프록시 서버</strong> : 클라이언트의 요청을 대신 받아서, 실제 서버(백엔드)에게 전달하고, 그 응답도 대신 받아서 클라이언트에게 다시 전달해주는 중간 서버</li>
<li><strong>로드 밸런서</strong> : 여러 서버에 트래픽을 균등하게 분산 -&gt; 서버 부하 방지</li>
<li><strong>HTTP 설정 (SSL 인증서)</strong> : 보안 통신을 위한 SSL 인증서 설정 가능</li>
</ol>
<hr />
<h2 id="2️⃣docker-rm--f-컨테이너명--컨테이너-삭제">2️⃣docker rm -f [컨테이너명] : 컨테이너 삭제</h2>
<h3 id="✅명령어-1">✅명령어</h3>
<pre><code class="language-bash">docker rm -f server</code></pre>
<h3 id="✅세부-설명-1">✅세부 설명</h3>
<ul>
<li><strong>docker rm [컨테이너명]</strong> : 컨테이너를 삭제하는 명령어</li>
<li><strong>-f</strong> : force(강제)의 약자로, <code>docker rm</code> 명령어에서 <strong>실행중인 컨테이너라도 강제로 종료하고 삭제</strong>하겠다는 뜻
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/8582791c-39e6-403e-bb0f-200653d92b57/image.png" /></li>
</ul>
<hr />
<h2 id="3️⃣nginx-컨테이너를-실행하면서-내가-만든-indexhtml-파일을-웹에-띄우기">3️⃣Nginx 컨테이너를 실행하면서 내가 만든 index.html 파일을 웹에 띄우기</h2>
<h3 id="✅명령어-2">✅명령어</h3>
<pre><code class="language-bash">docker run --name server -d -p 8080:80 -v ${PWD}/index.html:/usr/share/nginx/html/index.html nginx</code></pre>
<h3 id="✅세부-설명-2">✅세부 설명</h3>
<ul>
<li><strong>-v [덮어씌울파일]:[덮어씌워질파일]</strong> :  호스트의 파일이나 디렉토리를 컨테이너에 연결해, 데이터를 공유하거나 덮어씌우는 옵션. 
위 예제에는 ${PWD}/index.html:/usr/share/nginx/html/index.html를 명시해뒀는데, 이 뜻은 현재 디렉토리의 경로(${pwd})의 index.html 파일을 /usr/share/nginx/html/index.html에 덮어씌운다는 뜻이다.</li>
</ul>
<p><strong>➡️이름이 server인 nginx 웹 서버 컨테이너를 만들되, 내가 지정한 index.html 파일을 웹에 띄운다.</strong></p>
<h3 id="📍실행결과-1">📍실행결과</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/ee53cd83-8b52-4ac3-a5e0-a020faa0bd27/image.png" />-&gt;명령어 실행 전, cd 명령어로 index.html이 있는 폴더로 이동 해야함.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/b339e603-e757-4976-be74-b008d7c9b6bd/image.png" /></p>
<hr />
<h2 id="4️⃣nginx-컨테이너를-실행하면서-indexhtml-접근해서-수정하기">4️⃣Nginx 컨테이너를 실행하면서 index.html 접근해서 수정하기</h2>
<h3 id="✅명령어-3">✅명령어</h3>
<pre><code class="language-bash">docker exec -it server /bin/bash
cd /usr/share/nginx/html/
echo hello &gt; index.html</code></pre>
<h3 id="✅세부-설명-3">✅세부 설명</h3>
<ul>
<li><strong>docker exec</strong> : 실행중인 컨테이너 내부로 접속<ul>
<li><strong>-it [컨테이너명]</strong> : 해당 컨테이너의 터미널과 연결</li>
<li><strong>/bin/bash</strong> : 연결된 터미널에서 명령어를 실행</li>
</ul>
</li>
<li><strong>cd</strong> : 해당 디렉터리&amp;파일로 이동</li>
<li><strong>echo hello &gt; index.html</strong> : 문자열 hello를 index.html파일에 저장(기존 내용은 전부 덮어쓰기됨)</li>
</ul>
<p><strong>➡️이름이 server인 nginx 웹 서버 컨테이너를 만들되, index.html 파일의 내용을 hello로 변경한다.</strong></p>
<h3 id="📍실행결과-2">📍실행결과</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/00ce5082-601c-4b92-a7cf-3f639c45d3a1/image.png" />
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/67a23956-8556-4a9d-8abf-45c064c17c37/image.png" /></p>
<hr />
<h2 id="5️⃣mysql-컨테이너-만들기">5️⃣MySQL 컨테이너 만들기</h2>
<h3 id="✅명령어-4">✅명령어</h3>
<pre><code class="language-bash">docker run --name database -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=1234 -e MYSQL_DATABASE=backend mysql:8.0</code></pre>
<h3 id="✅세부-설명-4">✅세부 설명</h3>
<ul>
<li><strong>-e</strong> : 환경변수 설정(데이터 베이스 명, 패스워드)</li>
</ul>
<h3 id="📍실행결과-3">📍실행결과</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/36900601-de0d-43fd-be28-96aec299342f/image.png" /></p>
<blockquote>
<p>그러나 위 명령은 <strong>volume을 설정하지 않은 상태여서 컨테이너를 삭제하고 다시 생성하면 기존 컨테이너에 있던 데이터들이 사라진다.</strong></p>
</blockquote>
<hr />
<h2 id="6️⃣volume을-사용하여-mysql-컨테이너-만들기">6️⃣Volume을 사용하여 MySQL 컨테이너 만들기</h2>
<h3 id="✅명령어-5">✅명령어</h3>
<pre><code class="language-bash"> docker run --name database -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=1234 -e MYSQL_DATABASE=backend -v ${pwd}/volumes/mysql-data:/var/lib/mysql mysql:8.0 </code></pre>
<h3 id="✅세부-설명-5">✅세부 설명</h3>
<ul>
<li><strong>-v [호스트경로]:[컨테이너 경로]</strong> : Volume 설정하는 옵션. <ul>
<li>호스트 경로 : 내 컴퓨터 상의 폴더</li>
<li>컨테이너 경로 : 컨테이너 안에서 접근할 폴더</li>
</ul>
</li>
<li><blockquote>
<p>컨테이너가 사용하는 DB파일이 내 PC에도 그대로 저장됨.  </p>
</blockquote>
</li>
</ul>
<p><strong>➡️Volume을 설정해두면 컨테이너를 삭제해도 데이터가 유지된다.</strong></p>
<h3 id="📍실행결과-4">📍실행결과</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/366fbce7-259c-4ee9-aa98-b4143eed4314/image.png" />
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/4682e6c3-2b4b-4128-8e1f-3b32afb29b9e/image.png" /></p>
<ul>
<li>해당 경로에 volumes 폴더 생성됨</li>
</ul>
<hr />
<h2 id="7️⃣이미지컨테이너-생성하기">7️⃣이미지&amp;컨테이너 생성하기</h2>
<h3 id="1-backendproject-생성">1. backendProject 생성</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/18ded337-ec5d-451b-8d6c-cf2952dfc3f8/image.png" />
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/6da7118a-0f7b-4138-8da7-97a5b238d591/image.png" /></p>
<h3 id="2usercontrollerjava-작성">2.UserController.java 작성</h3>
<h4 id="comexamplebackendprojectusercontroller">com.example.backendproject.user.controller</h4>
<pre><code class="language-java">@RestController
@RequestMapping(&quot;/api&quot;)
public class UserController {

    //docker 서버 : 8080로 지정된 서버가 있으면 실행안됨
    @Value(&quot;${PROJECT_NAME:web server}&quot;)
    private String instansName;

    @GetMapping
    public String test(){
        return instansName;
    }
}</code></pre>
<h3 id="3-gradlew-clean-build">3. .\gradlew clean build</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/178528af-cef9-4ceb-973e-0de632b22a2e/image.png" /></p>
<ul>
<li>모든 이전 빌드 결과를 지우고 프로젝트를 새로 빌드</li>
<li>위 명령을 실행하면 build &gt; libs에 jar파일이 만들어짐
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/0e2d165c-ad57-4d21-8209-2fc21f157a68/image.png" /></li>
</ul>
<h3 id="4-프로젝트-하위에-dockerfile-생성-및-작성">4. 프로젝트 하위에 Dockerfile 생성 및 작성</h3>
<pre><code class="language-yml">#사용할 베이스 이미지 명시 (공식 이미지 권장)
FROM openjdk:17-jdk

#작업 디렉토리 생성 및 설정(컨테이너 내부 경로에 시작점으로 지정 -&gt; 없으면 생성 됨)
WORKDIR /app

#jar파일 등 빌드 결과물을 컨테이너에 복사 (호스트 -&gt; 컨테이너)
COPY build/libs/backendProject-0.0.1-SNAPSHOT.jar /app/backendProject-0.0.1-SNAPSHOT.jar

#컨테이너 실행시 기본으로 실행할 명령
CMD [&quot;java&quot;, &quot;-jar&quot;, &quot;/app/backendProject-0.0.1-SNAPSHOT.jar&quot;]</code></pre>
<h3 id="5-docker-build--t-backend-">5. docker build -t backend .</h3>
<ul>
<li>현재 디렉터리(.)에 있는 dockerfile을 이용해서 backend라는 이름의 Docker 이미지를 생성하는 명령어</li>
<li><strong>docker build</strong> : 이미지를 빌드하겠다는 명령</li>
<li><strong>-t backend</strong> : 생성할 이미지 이름을 backend로 지정</li>
<li><strong>.</strong> : 현재 디렉토리에서 Dockerfile을 찾음</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/c206981e-d710-41b9-a746-1a28bd41a988/image.png" /></p>
<h3 id="6-docker-images">6. docker images</h3>
<ul>
<li>내 컴퓨터에 저장된 도커 이미지 목록을 확인하는 명령어</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/4052387c-8f7c-4c02-822e-dd0351de946d/image.png" /></p>
<h3 id="7-dockr-run---name-backend--d--p-80508080-backend">7. dockr run --name backend -d -p 8050:8080 backend</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/394caa7c-3ab2-45a4-8f04-ac840ecb5bb2/image.png" /></p>
<h3 id="8-웹-확인">8. 웹 확인</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/52abf49b-9d9b-46c1-af07-1388ba31cb4f/image.png" /></p>
<h2 id="8️⃣도커-네트워크-구성해서-nginx--spring-서버-연동하기--리버스-프록시-서버">8️⃣도커 네트워크 구성해서 Nginx + Spring 서버 연동하기 : 리버스 프록시 서버</h2>
<h3 id="❓리버스-프록시-서버란">❓리버스 프록시 서버란?</h3>
<blockquote>
<p>클라이언트의 요청을 대신 받아서, 백엔드 서버(원래 목적지)에 전달하고, 그 응답을 다시 클라이언트에게 전달해주는 <strong>중간 서버</strong>를 뜻한다. 대표적인 리버스 프록시 중 하나가 Nginx이다.</p>
</blockquote>
<h3 id="1-도커-네트워크-구성">1. 도커 네트워크 구성</h3>
<pre><code class="language-bash">docker network create my-network</code></pre>
<ul>
<li>my-network라는 이름의 network 구성</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/fd607db3-8580-4545-9f0e-f5936dd19e66/image.png" /></p>
<h3 id="2-docker-run---name-backend--d--p-80808080---network-my-network-backend">2. docker run --name backend -d -p 8080:8080 --network my-network backend</h3>
<ul>
<li>my-network에 연결하는 backend 라는 이름을 가진 컨테이너 생성</li>
</ul>
<h3 id="3-현재-폴더에-nginx-폴더-생성-후-nginxconf-파일-작성">3. 현재 폴더에 nginx 폴더 생성 후 nginx.conf 파일 작성</h3>
<pre><code>worker_processes 1;

events {
    worker_connections 1024;
}

http {
    server {
        listen 80;

        location / {
            proxy_pass http://backend:8080/api;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}</code></pre><ul>
<li><strong>location /</strong> : /경로로 들어가면 매핑되는 항목들 설정</li>
<li><em>proxy_pass <a href="http://spring_backend/api">http://spring_backend/api</a>*</em> : /경로에 <a href="http://spring_backend/api">http://spring_backend/api</a> 주소를 매핑한다</li>
</ul>
<h3 id="4-backend5-폴더로-이동-후-컨테이너-생성">4. backend5 폴더로 이동 후 컨테이너 생성</h3>
<pre><code class="language-bash">cd C:\backend5
docker run --name nginx -d -p 80:80 -v ${pwd}/nginx/nginx.conf:/etc/nginx/nginx.conf --network my-network nginx</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/ef226f7c-5c7c-43fa-b2d0-7d36cdb60911/image.png" /></p>
<h3 id="5-웹에서-확인">5. 웹에서 확인</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/dc3e0109-4aab-4939-a704-3ba2983b7c47/image.png" /></p>
<hr />
<h1 id="💡docker-compose">💡Docker compose</h1>
<ul>
<li>여러개의 컨테이너를 한번에 정의하고 실행할 수 있게 도와주는 도구</li>
<li>하나의 설정파일(docker-compose.yml)로 여러 컨테이너 설정 관리</li>
<li>docker-compose up 명령어 한 줄로 전체 시스템 실행</li>
</ul>
<h2 id="✅도커-컴포즈를-이용해서-여러-컨테이너-만들기--로드밸런싱">✅도커 컴포즈를 이용해서 여러 컨테이너 만들기 : 로드밸런싱</h2>
<h3 id="1-dockercomposeyml-작성">1. dockercompose.yml 작성</h3>
<pre><code class="language-yml">services:

  backend1:  # 서비스 이름은 컨테이너간 통신하기 위한 이름
    image: backend
    container_name: backend1
    environment:
      PROJECT_NAME: 백앤드 서버1

  backend2:  # 서비스 이름은 컨테이너간 통신하기 위한 이름
    image: backend
    container_name: backend2
    environment:
      PROJECT_NAME: 백앤드 서버2

  backend3:  # 서비스 이름은 컨테이너간 통신하기 위한 이름
    image: backend
    container_name: backend3
    environment:
      PROJECT_NAME: 백앤드 서버3

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
<h3 id="2-nginxconf-파일-수정">2. nginx.conf 파일 수정</h3>
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
            proxy_pass http://spring_backend/api;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
}
</code></pre><ul>
<li><strong>location /</strong> : /경로로 들어가면 매핑되는 항목들 설정</li>
<li><em>proxy_pass <a href="http://spring_backend/api">http://spring_backend/api</a>*</em> : /경로에 <a href="http://spring_backend/api">http://spring_backend/api</a> 주소를 매핑한다</li>
</ul>
<h3 id="3-docker-compseyml이-있는-폴더에서-docker-compose-up-d">3. docker-compse.yml이 있는 폴더에서 docker compose up-d</h3>
<pre><code class="language-bash">docker compse up-d</code></pre>
<ul>
<li><strong>docker compse up</strong> : 작성한 docker-compose.yml을 바탕으로 여러 개의 컨테이너를 생성하여 시작할 때 사용하는 명령어</li>
<li><strong>-d</strong> : 백그라운드로 실행
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/e1c41fb5-5a80-4764-b24c-64140490d2fb/image.png" /></li>
</ul>
<h3 id="4-웹에서-확인">4. 웹에서 확인</h3>
<p><img alt="업로드중.." src="blob:https://velog.io/20058ebd-281d-4dfa-974e-303f24b83611" /></p>
<ul>
<li>localhost 로 접속하면 설정해둔 백엔드 서버 1,2,3이 구동되는 것 확인 가능</li>
</ul>
<p><strong>➡️도커의 로드밸런싱 기능 -&gt; 서버 부하 방지</strong></p>
<blockquote>
<h3 id="🔍로드밸런싱이란">🔍로드밸런싱이란?</h3>
<p>여러 개의 컨테이너(일반적으로 동일한 백엔드 애플리케이션)를 대상으로 트래픽을 분산시키는 기술을 의미한다.
일반적으로 하나의 컨테이너가 처리할 수 있는 요청량에는 한계가 있기 때문에, 여러 개의 컨테이너 인스턴스를 띄우고 그 사이에 균동하게 요청을 나누는 방식으로 확장성과 안정성을 높인다.
위 예시에서도 backend1, backend2, backend3 으로 컨테이너 3개를 나눠서 요청을 분산 시키고 있다.</p>
</blockquote>