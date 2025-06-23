<h1 id="💡구현한-채팅방을-젠킨스에-올려보자">💡구현한 채팅방을 젠킨스에 올려보자.</h1>
<blockquote>
<p>❓<strong>젠킨스란?</strong>
Jenkins는 <strong>CI/CD를 위한 오픈 소스 도구로, 소프트웨어 개발 생명주기를 자동화</strong>한다.
Jenkins를 사용하면 코드 변경 사항이 발생할 때마다 자동으로 빌드, 테스트, 배포 과정을 수행할 수 있기 때문에 <strong>개발자는 코드 변경에 따른 빌드 및 테스트 과정을 수동으로 수행할 필요가 없어지며</strong>, <strong>신속하게 개발에 대한 코드 검증 및 배포</strong>할 수 있고, 이 과정에서 휴먼 에러를 방지할 수 있다.</p>
</blockquote>
<h2 id="✅-실습">✅ 실습</h2>
<h3 id="1-backned5-폴더-하위에-cicdjenkins-폴더-및-파일-생성">1. backned5 폴더 하위에 CICD/jenkins 폴더 및 파일 생성</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/80d15149-2785-4cb3-8870-f0a3b2c2bfd4/image.png" /></p>
<h3 id="2-jenkins-폴더로-이동-후-compose-up">2. jenkins 폴더로 이동 후 compose up</h3>
<pre><code class="language-bash">cd CICD\jenkins
docker compose up -d</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/8e434598-863d-42a3-8c0a-348a4c424398/image.png" /><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/5451439a-84da-4a4c-a6e8-b3b5df170969/image.png" /></p>
<h3 id="3-localhost7070으로-접속">3. localhost:7070으로 접속</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/04eb33d3-fd5a-4c8e-8108-925dd5be992b/image.png" /></p>
<h3 id="4-젠킨스-초기-비밀번호-추출">4. 젠킨스 초기 비밀번호 추출</h3>
<ul>
<li>젠킨스 폴더 경로에서 명령어 입력<pre><code class="language-bash">docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword</code></pre>
</li>
</ul>
<h3 id="5-4의-비밀번호-입력">5. 4의 비밀번호 입력</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/bf856233-48f5-40a0-bebe-28e0b70a06ca/image.png" /></p>
<h3 id="6-install-suggested-plugins-클릭">6. Install suggested plugins 클릭</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/6b1b6f48-ad8f-4e13-8385-05ece9da5bae/image.png" /></p>
<h3 id="7-설치-기다리기">7. 설치 기다리기</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/c11001bc-d490-49d8-b9d3-9f5d83227aba/image.png" /><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/14a59839-146c-4ce5-83cd-929906846246/image.png" /><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/0ea98458-e538-4936-aeb4-bbd09b9cb48b/image.png" /><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/d38d8f0d-617b-4a46-9b59-f64f88c9cd60/image.png" /></p>
<h3 id="8-new-item-클릭">8. new Item 클릭</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/ae682d36-a5c1-47a0-b758-d59cc7c99d0e/image.png" /></p>
<h3 id="9-jenkins-관련-파일에-적혀있는-이름으로-item명-정하기">9. jenkins 관련 파일에 적혀있는 이름으로 item명 정하기</h3>
<ul>
<li><p>jenkinsfile
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/1c142f19-900a-497d-9c68-1775cb3d7aac/image.png" /></p>
</li>
<li><p>docker-compose.yml
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/4559b5e0-4a80-4fa1-9185-35d25f4f99b4/image.png" /></p>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/0b7b4262-e1e5-4d72-9387-d0ea90c9c959/image.png" /></p>
<h3 id="10-제대로-적용-됐는지-확인">10. 제대로 적용 됐는지 확인</h3>
<pre><code class="language-bash">docker exec -t jenkins /bin/bash
root@d0aa88ae23fb:/var/jenkins_home/workspace# ls
backend5_Test_local  backend5_local
root@d0aa88ae23fb:/var/jenkins_home/workspace# cd backend5_Test_local/
root@d0aa88ae23fb:/var/jenkins_home/workspace/backend5_Test_local# ls
CICD  backendProject  docker-compose.yml  nginx  volumes
root@d0aa88ae23fb:/var/jenkins_home/workspace/backend5_Test_local#</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/0d1a4ea8-90a4-45c0-8583-1de94f137000/image.png" /></p>
<h3 id="11-nginx폴더-하위에-dockerfile-생성">11. nginx폴더 하위에 Dockerfile 생성</h3>
<pre><code>FROM nginx:latest

#젠킨스에 올릴 때, 우리가 만든 설정파일이 설정되지않음
#-&gt; 여기서 nginx.conf복사를 해서 강제적으로 덮어씌운다.

#기존 설정 제거(선택적)
RUN rm /etc/nginx/nginx.conf

#커스텀 nginx.conf 복사
COPY ./nginx.conf /etc/nginx/nginx.conf</code></pre><h3 id="12-프로젝트-폴더-밖-docker-composeyml-수정">12. 프로젝트 폴더 밖 docker-compose.yml 수정</h3>
<pre><code class="language-yml">  nginx:
    build: ./nginx/.
    ports:
      - &quot;80:80&quot;
    # volumes:
    # - /.nginx/nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - backend1
      - backend2
      - backend3</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/85bc56f4-b6f3-434c-abc2-ce559fea8035/image.png" /></p>
<h3 id="13-h2-의존성-추가">13. h2 의존성 추가</h3>
<pre><code class="language-bash">testImplementation 'com.h2database:h2'</code></pre>
<h3 id="14-application-testproperties-생성">14. application-test.properties 생성</h3>
<pre><code>spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect</code></pre><h3 id="15-backendprojectapplicationtests-수정">15. BackendProjectApplicationTests 수정</h3>
<pre><code class="language-java">@ActiveProfiles(&quot;test&quot;)//요거추가
@SpringBootTest
class BackendProjectApplicationTests {

    @Test
    void contextLoads() {
    }

}</code></pre>
<h3 id="16-redisconfigjava에-어노테이션-추가">16. redisConfig.java에 어노테이션 추가</h3>
<pre><code class="language-java">@Profile(&quot;!test&quot;) //테스트 시 redis는 무시한다.</code></pre>
<h3 id="17-젠킨스-홈페이지에서-빌드">17. 젠킨스 홈페이지에서 빌드</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/a9613c6b-2bce-4eb8-873e-90d3d5015dfc/image.png" /></p>
<ul>
<li>jenkinsfile 복사 붙여넣기 후 save
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/20aa18b4-4afc-4f96-9885-b219946bc45a/image.png" /></li>
</ul>
<h3 id="18-빌드-확인">18. 빌드 확인</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/04ad8f59-7591-4653-b83b-8de3ba7dd453/image.png" />
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/ee93283c-e654-4d19-a210-8c91035cb0fc/image.png" /></p>
<h3 id="19-웹에서-확인">19. 웹에서 확인</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/053af1c5-95dd-4fb8-9629-2e47152018a3/image.png" /></p>