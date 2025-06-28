<h1 id="🤔로그-처리란">🤔로그 처리란?</h1>
<blockquote>
<p>로그 처리는 <strong>애플리케이션 실행 중 발생하는 주요 정보, 경고, 오류 등을 기록하여 문제를 추적하고 시스템 동작을 분석</strong>할 수 있게 해주는 작업이다. 스프링 부트에서는 보통 <code>SLF4J</code>와 <code>LogBack</code>을 기반으로 로그를 처리한다.</p>
</blockquote>
<h2 id="🤨그렇다면-왜-로그를-남겨야하는데">🤨그렇다면 왜 로그를 남겨야하는데?</h2>
<blockquote>
<p>로그를 남겨야 하는 이유는 여러가지가 있다.</p>
</blockquote>
<p><strong>1. 에러/장애 추적</strong></p>
<ul>
<li>시스템이 오류를 발생시켰을 때 원인을 분석할 유일한 단서이다.</li>
<li>예를 들어, 사용자 로그인시 500에러가 발생했다면, 로그에 남긴 userId, 요청 IP, 스택 트레잇 등을 보고 빠르게 문제를 재현 및 해결할 수 있다.</li>
</ul>
<p><strong>2. 운영/모니터링</strong></p>
<ul>
<li>서버의 상태를 실시간으로 감시하거나 이상 동작을 감지하기 위해 필요하다.</li>
<li>예를 들어, CPI 부하가 급증하거나, 특정 API가 평소보다 과도하게 호출되는 경우 로그로 파악이 가능하다.</li>
</ul>
<p><strong>3. 보안 감시 및 기록</strong></p>
<ul>
<li>로그인 시도, 인증 실패, 관리자 접근 등 보안 이벤트 기록은 로그 처리가 필수다.</li>
<li>예를 들어, 로그인 싪 로그를 통해 무차별 대입 공격(Brute Force Attack) 탐지가 가능하다.</li>
</ul>
<p><strong>4. 디버깅 및 개발</strong></p>
<ul>
<li>개발 중 코드 흐름을 확인하거나, 정상적인 데이터 흐름을 추적하는데 필수다.</li>
<li>로그를 통해 Controller → Service → Repository로 흐르는 중간 값이 어디서 잘못됐는지 확인이 가능하다.</li>
</ul>
<p><strong>5. 이벤트 감사 및 회귀 분석</strong></p>
<ul>
<li>사용자 행위 로그(게시글 작성, 삭제 등)을 통해 사후 분석 가능</li>
</ul>
<blockquote>
<p>➡️ 코드나 에러를 직접 볼 수 없는 운영 환경에서, <strong>로그는 개발자에게 시스템을 이해하고 문제를 해결하는 유일한 도구</strong>이다.
→ 따라서 <strong>중요한 흐름, 경고, 예외</strong>는 반드시 로그로 남기는 습관이 필요하다.</p>
</blockquote>
<hr />
<h2 id="✅로그-레벨">✅로그 레벨</h2>
<blockquote>
<p><strong>로그의 중요도에 따라 분류한 단계</strong>로, 개발자가 로그 메시지를 출력할 때 어떤 수준(Level)으로 남길지를 지정함으로써, 필요한 로그만 필터링하고 볼 수 있게 해준다.</p>
</blockquote>
<table border="1">
  <thead>
    <tr>
      <th>레벨</th>
      <th>설명</th>
      <th>사용 목적</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><b>TRACE</b></td>
      <td>가장 상세한 로그, 모든 내부 흐름을 추적</td>
      <td>미세한 디버깅용. 일반적으로 개발 중에만 사용</td>
    </tr>
    <tr>
      <td><b>DEBUG</td>
      <td>디버깅 정보, 변수 값 추적</td>
      <td>비즈니스 로직이나 흐름을 이해하는 데 유용</td>
    </tr>
    <tr>
      <td><b>INFO</td>
      <td>일반 정보, 상태 확인</td>
      <td>정상적인 흐름을 기록. 운영 환경에서 주로 사용</td>
    </tr>
    <tr>
      <td><b>WARN</td>
      <td>경고 상황. 치명적이진 않지만 주의 필요</td>
      <td>예: API 응답 느림, 예상치 못한 데이터</td>
    </tr>
    <tr>
      <td><b>ERROR</td>
      <td>오류 발생. 시스템 일부 실패</td>
      <td>예외, 서비스 실패 등 명확한 문제 발생</td>
    </tr>

  </tbody>
</table>

<p>➡️운영환경에서는 보통 <code>INFO</code> 또는 <code>WARN</code> 레벨 이상만 출력하도록 설정</p>
<hr />
<h3 id="📍applicationyml-설정-예시">📍application.yml 설정 예시</h3>
<pre><code class="language-bash">logging.level.root=info            #전체 로그 레벨 설정
logging.level.com.example=debug    #패키지별 로그 레벨 설정
logging.file.name=logs/app.log    #파일 로그 저장 경로
logging.pattern.console=%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %highlight([%-3level]) %cyan(%logger{5}) - %msg%n    #콘솔 로그 출력 형식 지정 패턴</code></pre>
<ul>
<li>콘솔 로그 출력 형식대로 로그가 찍힌다<pre><code class="language-bash">2025-06-28 01:10:32.456 [http-nio-8080-exec-2] [INF] o.e.b.s.AuthService - 로그인 성공 - userId=ssj</code></pre>
</li>
</ul>
<h3 id="📍로그-라이브러리-설정-예시">📍로그 라이브러리 설정 예시</h3>
<pre><code class="language-java">@Slf4j    //로깅 라이브러리
@RestController
public class SampleController {
    @GetMapping(&quot;/log-test&quot;)
    public String logTest() {
        log.trace(&quot;TRACE 레벨 로그&quot;);
        log.debug(&quot;DEBUG 레벨 로그&quot;);
        log.info(&quot;INFO 레벨 로그&quot;);
        log.warn(&quot;WARN 레벨 로그&quot;);
        log.error(&quot;ERROR 레벨 로그&quot;);
        return &quot;로그 확인&quot;;
    }
}</code></pre>
<hr />
<h1 id="🤔elk란">🤔ELK란?</h1>
<blockquote>
<p><strong>데이터 처리 관련 오픈소스 솔루션인 엘라스틱 서치(Elasticsearch) + 로그 스태시(Logstash) + 키바나(Kibana)를 같이 연동하여 사용</strong>한다는 의미로, ELK 혹은 ELK 스택(ELK Stack), 엘라스틱 스택(Elastic Stack)이라고 한다.
세 모듈은 각자 독립된 기술이라 일부만 사용할 수 있지만, 서로 호환이 잘되고 함께 사용하면 시너지가 좋기 때문에 세 기술을 함께 사용한다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/8b5068b7-4117-4cc3-b7fe-1841db7657e2/image.png" /></p>
<hr />
<h2 id="🔍elasticsearch-로그-검색-및-저장">🔍Elasticsearch (로그 검색 및 저장)</h2>
<ul>
<li><code>Elasticsearch</code>는 <strong>실시간으로 데이터를 저장하고, 검색, 집계할 수 있는 분산 검색 엔진</strong>이다.</li>
<li>검색 속도가 매우 빠르고, 대량의 데이터도 수 초 내 검색 가능하다.</li>
</ul>
<h2 id="🛠️-logstash-로그-수집-및-변환">🛠️ Logstash (로그 수집 및 변환)</h2>
<ul>
<li><code>Logstash</code>는 오픈소스 서버측 데이터 처리 파이프라인으로, <strong>다양한 소스에서 동시에 데이터를 수집하고 변환</strong>하여 stash 보관소로 보낸다.</li>
<li>수집할 로그를 선정해서 지정된 대상 서버(<code>Elasticsearch</code>)에 인덱싱하여 전송하는 역할을 담당한다.</li>
</ul>
<h2 id="📊-kibana로그-시각화-및-관리">📊 Kibana(로그 시각화 및 관리)</h2>
<ul>
<li><code>Elasticsearch</code>에 저장된 <strong>데이터를 시각화하여 대시보드로 구성하고 모니터링</strong>할 수 있다.</li>
</ul>