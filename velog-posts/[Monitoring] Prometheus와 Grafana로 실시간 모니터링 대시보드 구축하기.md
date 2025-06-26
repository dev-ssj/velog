<h1 id="🤔prometheus랑-grafana가-뭐지">🤔Prometheus랑 Grafana가 뭐지?</h1>
<blockquote>
<p>Prometheus와 Grafana는 서로 보완적으로 작동하는 모니터링 시스템 구성의 핵심 도구라고 할 수 있다.
<strong>Prometheus는 매트릭을 수집하고 저장하는 시스템</strong>이고, <strong>Grafana는 프로메테우스에 저장된 데이터를 시각화</strong>하는 Tool이다.
프로메테우스에서도 데이터를 확인할 수 있지만, 그라파나를 이용하여 시각화해서 모니터링 하는 게 보편적이다.</p>
</blockquote>
<blockquote>
<p><strong>❓매트릭이란?</strong>
<code>매트릭(Metric)</code>은 모니터링, 성능 분석, 로깅, 운영 지표 수집 등에서 <strong>시스템 상태를 수치화한 지표</strong>를 의미한다.
이 정보들은 시스템의 헬스체크, 성능 튜닝, 장애 조치, 서비스 운영 최적화 등 여러 목적으로 활용되며, 매트릭에서 수집가능한 정보들은 다음과 같다.</p>
</blockquote>
<ul>
<li>CPU 사용률, 메모리 사용량, GC 횟수, 스레드 수</li>
<li>HTTP 요청 수, 응답 시간, 에러 발생 수, DB 커넥션 수</li>
<li>사용자 정의 비즈니스 지표(결제건수, 가입자 수 등)</li>
</ul>
<hr />
<h1 id="✅prometheus와-grafana로-실시간-모니터링-대시보드-구축하기">✅Prometheus와 Grafana로 실시간 모니터링 대시보드 구축하기</h1>
<h2 id="1️⃣의존성-추가">1️⃣의존성 추가</h2>
<pre><code class="language-bash">//Spring Boot Actuator   매트릭/모니터링
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    //프로메테우스
    implementation 'io.micrometer:micrometer-registry-prometheus'</code></pre>
<ul>
<li>스프링에서는 actuator 라이브러리만 추가하면 다양한 매트릭을 자동으로 수집할 수 있다.</li>
</ul>
<h2 id="2️⃣backend5폴더에-monitoring폴더-추가">2️⃣backend5폴더에 monitoring폴더 추가<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/4f122c89-6c1d-498f-8fc7-49867ccf619e/image.png" /></h2>
<h2 id="3️⃣applicationproperties에-엔드포인트-추가">3️⃣application.properties에 엔드포인트 추가</h2>
<pre><code class="language-bash">#actuator and metric and prometheus
#prometheus 전용 앤드포인트 생성
management.prometheus.metrics.export.enabled=true
#prometheus 엔드포인트를 노출
management.endpoints.web.exposure.include=*
#앤드 포인트에서 어떤 정보를 보여줄지 설정
management.endpoint.health.show-details=always
#never -&gt; 절대 상세 정보 제공 안함 (항상 status만 응답)
#when-authorized -&gt; 인증된 사용자/로컬 요청에만 상세 정보 제공 (기본값, 권장)
#always -&gt; 항상 상세 정보 포함 (외부/내부 관계없이 상세 정보 노출, 개발·테스트용)</code></pre>
<h2 id="4️⃣monitoring--http-폴더에-cd로-접근하여-docker-compose-up">4️⃣monitoring &gt; http 폴더에 cd로 접근하여 docker compose up</h2>
<blockquote>
<p>docker compose -f docker-compose.monitoring.yml up -d </p>
</blockquote>
<h2 id="5️⃣컨테이너-생성-확인">5️⃣컨테이너 생성 확인</h2>
<blockquote>
<p>컨테이너가 잘 생성되었는지 아래 포트로 접속해서 확인해보자</p>
</blockquote>
<h3 id="📍localhost9090---프로메테우스">📍localhost:9090 -&gt; 프로메테우스<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/c0981003-4e8a-429d-8133-fc35a1d32f0b/image.png" /></h3>
<h3 id="📍localhost3000---그라파나">📍localhost:3000 -&gt; 그라파나<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/b8fed0dc-dc31-43b8-9046-ada99517ef5a/image.png" /></h3>
<h2 id="6️⃣프로메테우스--연결-확인">6️⃣프로메테우스 : 연결 확인</h2>
<h3 id="6-1-status--target-health">6-1. [Status] &gt; [Target health]<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/e4e19767-d9a9-4e35-a734-1816e570b9ac/image.png" /></h3>
<h3 id="6-2-hostdockerinternal8080-state-up-확인">6-2. host.docker.internal:8080 State Up 확인<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/73c23ec1-94aa-46ae-9066-a49f76b711bd/image.png" /></h3>
<pre><code class="language-bash">- targets:
    - 'host.docker.internal:8080' #도커 컨테이너가 로컬 호스트를 바라보는 공식 DNS 이름
    - 'localhost:8080'</code></pre>
<blockquote>
<p> <strong>🔍localhost:8080이 에러가 뜨는 이유</strong>
프로메테우스는 현재 도커 컨테이너 안에서 실행중이고, 스프링 부트 서버는 localhost:8080으로 실행중이다. 이 경우, localhost는 프로메테우스 컨테이너 자기자신을 의미하지만, 프로메테우스 컨테이너 안에는 스프링부트 서버가 없으므로 에러가 발생한다.
따라서, <strong>도커 컨테이너 내부에서 호스트에 접근을 해야하므로 공식적으로 제공되는 DNS 이름인 host.docker.internal</strong>을 써야함!
즉, host.docker.internal은 도커 컨테이너 안에서 호스트 PC(localhost)에 접근하는 DNS이고, 현재 호스트에서 스프링부트 서버가 8080포트로 동작 중이므로 <code>host.docker.internal:8080</code>으로 접근해야한다.</p>
</blockquote>
<h2 id="7️⃣그라파나에서-데이터소스-생성하기">7️⃣그라파나에서 데이터소스 생성하기</h2>
<blockquote>
<p>프로메테우스까지 설정했으면 이제 프로메테우스에서 가져온 매트릭을 그라파나에서 이용할 수 있도록 데이터소스를 생성해 보자.</p>
</blockquote>
<h3 id="7-1-그라파나-로그인">7-1. 그라파나 로그인<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/b8fed0dc-dc31-43b8-9046-ada99517ef5a/image.png" /></h3>
<ul>
<li>초기 아이디 admin, 비밀번호 admin</li>
</ul>
<h3 id="7-2-connections--data-source--add-data-source">7-2. [Connections] &gt; [Data source] &gt; [Add data source]<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/44385459-9883-4931-9bd7-d4ad4aba40cc/image.png" /></h3>
<h3 id="7-3-data-source의-name과-프로메테우스-서버-url-적어-준-후-save--test-클릭">7-3. data source의 name과 프로메테우스 서버 URL 적어 준 후 Save &amp; test 클릭<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/b0cf4587-9728-43d7-a6bd-9c60c8f5c50e/image.png" /></h3>
<ul>
<li>프로메테우스 서버 URL 위의 프로메테우스 설정 시 접속했던 URL(<strong>localhost:9090</strong>)으로 적어줘야한다.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/d3b985b3-6dc0-4238-9a5e-27efd4715571/image.png" /></p>
<ul>
<li>제대로 적어줬다면 하단 Save &amp; test를 눌렀을 때 Successfully가 뜬다. </li>
</ul>
<h2 id="8️⃣dashboard-생성하기">8️⃣Dashboard 생성하기</h2>
<blockquote>
<p>마지막으로 위의 데이터 소스에서 가져온 매트릭을 Dashboard로 생성해서 시각화 해보도록 하자.</p>
</blockquote>
<h3 id="8-1-dashboards--new-dashboard--save-dashboard">8-1. [Dashboards] &gt; [New dashboard] &gt; [Save dashboard]<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/0fe4ce4e-0907-46b2-85c5-a00e31aa38e5/image.png" /></h3>
<h3 id="8-2-grafanacomdashboards-에서-원하는-대시보드-가져오기">8-2. grafana.com/dashboards 에서 원하는 대시보드 가져오기</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/e73ed4a0-2ede-44f9-b7b7-853b0f05b751/image.png" /></p>
<h3 id="8-3-위에서-가져온-대시보드-json-파일을-업로드하고-프로메테우스-란에-아까-만든-datasource-선택">8-3. 위에서 가져온 대시보드 json 파일을 업로드하고, 프로메테우스 란에 아까 만든 datasource 선택 <img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/7350e136-58d6-4c48-a91e-a79e1315eb09/image.png" /></h3>
<h2 id="9️⃣대시보드-확인">9️⃣대시보드 확인<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/51ae0406-f0bb-4a45-83d3-2afc884cd324/image.png" /></h2>
<ul>
<li>내가 만든 프로젝트와 연동이 잘 되는 것 확인!</li>
</ul>