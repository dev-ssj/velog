<h1 id="🤔예외exception란">🤔예외(Exception)란?</h1>
<blockquote>
<p>예외란 <strong>프로그램 실행 중에 발생하는 비정상적인 상황</strong>을 의미하며, 정상적인 흐름을 방해하는 사건이다. <strong>개발자가 예측하고 처리 할 수 있는 문제 상황</strong>으로, 예외와 에러와는 다르다!
<strong>에러는 JVM 자체 문제등 개발자가 처리 불가능한 문제</strong>를 의미!</p>
</blockquote>
<hr />
<h1 id="🧐예외처리exception-handling는">🧐예외처리(Exception Handling)는?</h1>
<blockquote>
<p><strong>프로그램 실행 중 발생할 수 있는 예외적인 상황을 처리하는 방식</strong>을 의미하며, 에외를 적절히 처리하지 않으면 프로그램이 비정상적으로 종료되거나 보안 취약점, 데이터 손실, 이상 동작 등을 유발할 수 있다.</p>
</blockquote>
<hr />
<h2 id="✅-예외처리가-중요한-이유">✅ 예외처리가 중요한 이유</h2>
<ul>
<li>프로그램의 비정상 종료 방지</li>
<li>정확한 오류 원인 전달</li>
<li>로그 기록 및 모니터링 </li>
<li>사용자에게 친절한 피드백 제공(UX)</li>
<li>안정적인 트랜잭션 처리</li>
</ul>
<hr />
<h2 id="✅-예외를-구분해서-처리해야-하는-이유">✅ 예외를 구분해서 처리해야 하는 이유</h2>
<ul>
<li>예외마다 문제의 원인이 다르기 때문</li>
<li>각 예외마다 HTTP 상태 코드를 매핑</li>
<li>비즈니스 로직에 따라 예외 메시지와 후처리가 달라짐</li>
</ul>
<hr />
<h2 id="✅예외의-종류">✅예외의 종류</h2>
<blockquote>
<p>자바 예외 시스템에서 예외의 종류는 <code>Checked</code>와 <code>UnChecked</code> 두가지로 나눌 수 있다.</p>
</blockquote>
<pre><code>Throwable
├── Error                 // 시스템 오류 (개발자 처리 불가)
└── Exception             // 애플리케이션 예외 (개발자가 처리 가능)
    ├── Checked Exception // 반드시 처리 필요 (try-catch or throws)
    └── Unchecked Exception (RuntimeException) // 선택적 처리</code></pre><hr />
<h3 id="📌checked-exception">📌Checked Exception</h3>
<blockquote>
<p><strong>컴파일 시점에 반드시 처리해야하는 예외</strong>로, try-catch나 throws로 반드시 예외처리를 해야한다. <strong>예외처리를 하지 않으면 컴파일 에러가 발생하고 아예 실행이 되지 않는다.</strong>
대표적인 예외로는 <code>IOException</code>, <code>SQLException</code>, <code>ParseException</code>, <code>ClassNotFoundException</code> 등이 있다.</p>
</blockquote>
<h3 id="📌unchecked-exception">📌UnChecked Exception</h3>
<blockquote>
<p><strong>런타임(실행) 중 발생하며 컴파일러가 신경 쓰지 않는 예외</strong>로, 해당 요청만 실패로 처리하고 다음 요청을 진행하며 <strong>예외처리를 하지 않아도 서버가 죽지 않는다.</strong><br />대표적인 예외로는 <code>NullPointerException</code>, <code>IllegalArgumentException</code>, <code>IndexOutOfBoundsException</code>, <code>ArithmeticException</code> 등이 있다.</p>
</blockquote>
<hr />
<h1 id="💡글로벌-예외처리를-해주는-globalexceptionhandler를-만들어보자">💡글로벌 예외처리를 해주는 GlobalExceptionHandler를 만들어보자</h1>
<blockquote>
<p>예외처리에는 여러 방법이 있지만 <code>GlobalExceptionHandler</code>를 만들어 글로벌 예외처리를 하는것이 가장 보편적인 방법이다.
<code>GlobalExceptionHandler</code>를 사용해 예외처리를 해보자.</p>
</blockquote>
<hr />
<h2 id="1-globalexceptionhandlerjava-생성">1. GlobalExceptionHandler.java 생성</h2>
<pre><code class="language-java">@Slf4j
@RestControllerAdvice
//스프링에서 모든 컨트롤러의 예외를 한곳에서 처리하기 위한 어노테이션
//글로벌 예외처리기가 없으면 우리가 이전에 보던 장황한 에러코드가 모두 보임
public class GlobalExceptionHandler {

    //컨트롤러에서 RuntimeException 에러가 발생했을때 이 메서드가 대신 처리하도록 매핑
     @ExceptionHandler(RuntimeException.class)
    public ResponseEntity&lt;ErrorResponse&gt; handleRuntimeException(RuntimeException e){
        log.warn(&quot;런타임 예외 처리: {}&quot;, e.getMessage());
        ErrorResponse errorResponse = new ErrorResponse(400, &quot;에러 전달 메시지&quot;, e.getMessage());
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse);
    }
}</code></pre>
<ul>
<li><strong>@ControllerAdvice</strong> : 모든 컨트롤러에서 발생하는 예외를 가로채서 처리하는 클래스임을 명시. → 즉, 모든 예외는 여기서 처리된다.
반환값은 기본적으로 View</li>
<li><strong>@RestControllerAdvice</strong> : <code>@ControllerAdvice</code> + <code>@ResponseBody</code>의 조합으로, 반환값은 JSON 형태로 자동 직렬화되어 응답한다.</li>
<li><strong>@ExceptionHandle</strong> : 스프링에서 특정 예외가 발생했을 때, 그 예외를 처리할 메서드를 지정하는 어노테이션</li>
</ul>
<h2 id="2-errorresponse-생성">2. ErrorResponse 생성</h2>
<pre><code class="language-java">  @Getter
  @AllArgsConstructor
  public class ErrorResponse {
      private int code;   //상태코드
      private String message; //커스텀 에러 메시지
      private String detail;  //실제 에러 메세지
  }</code></pre>
<ul>
<li>ErrorResponse는 예외 발생 시 <strong>클라이언트에게 에러 정보를 전달</strong>하기 위해 사용하는 응답 객체로 쓰인다.</li>
</ul>
<h2 id="3-예외처리-확인하기">3. 예외처리 확인하기</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/8e19c5b0-eec5-4f37-a3f7-57d56f4dff36/image.png" /></p>
<pre><code class="language-bash">2025-06-27T10:11:10.135+09:00  WARN 5620 --- [backendProject] [nio-8080-exec-1] o.e.b.exception.GlobalExceptionHandler   : 런타임 예외 처리: 해당 유저를 찾을 수 없습니다.
2025-06-27T10:11:10.144+09:00  INFO 5620 --- [backendProject] [  restartedMain] o.e.b.BackendProjectApplication          : Started BackendProjectApplication in 7.304 seconds (process running for 7.941)
2025-06-27T10:11:10.164+09:00  WARN 5620 --- [backendProject] [nio-8080-exec-1] .m.m.a.ExceptionHandlerExceptionResolver : Resolved [java.lang.RuntimeException: 해당 유저를 찾을 수 없습니다.]</code></pre>
<ul>
<li>로그를 보면 위처럼 출력되는 것을 볼 수 있다. 한줄한줄 살펴보자.<br /></li>
</ul>
<pre><code class="language-bash">2025-06-27T10:11:10.135+09:00  WARN 5620 --- [backendProject] [nio-8080-exec-1] o.e.b.exception.GlobalExceptionHandler   : 런타임 예외 처리: 해당 유저를 찾을 수 없습니다.</code></pre>
<ul>
<li><code>GlobalExceptionHandler</code>클래스를 이용해서 예외처리한 로그</li>
<li><code>로그레벨</code> : WARN</li>
<li><code>메시지</code> : 런타임 예외 처리 : 해당 유저를 찾을 수 없습니다 
→ 예외 핸들러 안에서 log.warn()으로 출력한 메세지</li>
</ul>
<pre><code class="language-bash">2025-06-27T10:11:10.144+09:00  INFO 5620 --- [backendProject] [  restartedMain] o.e.b.BackendProjectApplication          : Started BackendProjectApplication in 7.304 seconds (process running for 7.941)</code></pre>
<ul>
<li>스프링부트 애플리케이션이 정상적으로 시작됐다는 로그</li>
</ul>
<pre><code class="language-bash">2025-06-27T10:11:10.164+09:00  WARN 5620 --- [backendProject] [nio-8080-exec-1] .m.m.a.ExceptionHandlerExceptionResolver : Resolved [java.lang.RuntimeException: 해당 유저를 찾을 수 없습니다.]</code></pre>
<ul>
<li>Spring 내부의 <code>ExceptionHandlerExceptionResolver</code>가 예외를 성공적으로 처리했다는 의미의 로그</li>
<li><code>Resolved</code> : 예외가 더이상 전파되지 않고 여기서 처리되었다는 뜻</li>
<li><code>발생한 예외</code> : RuntimeException</li>
</ul>
<hr />
<h2 id="🔁-예외처리-흐름-정리">🔁 예외처리 흐름 정리</h2>
<ol>
<li>서비스나 레포지토리 계층에서 <code>throw new RuntimeException(&quot;해당 유저를 찾을 수 없습니다.&quot;);</code> 또는 <code>.orElseThrow(()-&gt;new RuntimeException(&quot;해당 유저를 찾을 수 없습니다.&quot;));</code> 발생</li>
<li>컨트롤러 단에서 이 예외를 받지 않고 전파 →** @RestControllerAdvice** 또는 <strong>@ExceptionHandler</strong>로 등록된 예외처리기가 있으면 여기로 전파된다!</li>
<li><code>@RestControllerAdvice</code>로 정의된 <code>GlobalExceptionHandler</code>에서 이 <code>RuntimeException</code>을 감지하고 처리</li>
<li>로그로 남기고 <code>ErrorResponse</code> 형태로 응답 반환</li>
<li>Spring 내부 예외 리졸버(<code>ExceptionHandlerExceptionResolver</code>)가 이 처리를 마무리하고 로그 남김</li>
</ol>
<hr />
<h2 id="❓글로벌-예외처리기를-사용하지-않으면">❓글로벌 예외처리기를 사용하지 않으면?</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/6fae33a4-62ac-4366-9af4-7645a228e057/image.png" /></p>
<pre><code class="language-bash">java.lang.RuntimeException: 해당 유저를 찾을 수 없습니다.
    at org.example.backendproject.Auth.service.AuthService.lambda$login$0(AuthService.java:71) ~[main/:na]
    at java.base/java.util.Optional.orElseThrow(Optional.java:403) ~[na:na]
    at org.example.backendproject.Auth.service.AuthService.login(AuthService.java:71) ~[main/:na]
    at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:na]
    at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:77) ~[na:na]
    at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:na]
    at java.base/java.lang.reflect.Method.invoke(Method.java:569) ~[na:na]
    at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:359) ~[spring-aop-6.2.7.jar:6.2.7]
...</code></pre>
<ul>
<li>위와 같이 평소에 우리가 보던 장황한 전체 스택레이스를 확인할 수 있다.</li>
</ul>
<blockquote>
<p>📍<strong>왜 이런 장황한 스택레이스가 뜨는가?</strong>
글로벌 예외 처리를 안 하면, Spring MVC는 아래와 같이 동작한다.</p>
</blockquote>
<ol>
<li><code>RuntimeException</code>이 던져짐 (예: <code>Optional.orElseThrow(...)</code>)</li>
<li><code>@ExceptionHandler</code>가 없으면 → <strong>Spring이 기본 처리 수행</strong></li>
<li><code>DispatcherServlet</code>이 예외를 캐치하고 콘솔에 <strong>스택트레이스를 전부 출력</strong></li>
<li>기본적으로 HTML 에러 페이지 (Whitelabel Error Page) 또는 그냥 500 에러 응답만 반환</li>
</ol>