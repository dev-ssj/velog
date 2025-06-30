<h1 id="💡스레드-로컬threadlocal이란">💡스레드 로컬(ThreadLocal)이란?</h1>
<ul>
<li>각 스레드마다 독립적인 변수 값을 유지할 수 있도록 해주는 Java의 기능</li>
<li>스레드마다 자기만의 값을 저장하고 읽기 떄문에 다른 스레드와 값을 공유하지 않는다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/bb5f6944-b952-4911-8a3a-1fbc2e331da4/image.png" /></li>
</ul>
<hr />
<h2 id="💭스레드-로컬은-언제-사용할까">💭스레드 로컬은 언제 사용할까?</h2>
<ol>
<li>로그 추적(traceID)</li>
<li>인증/인가(로그인 사용자 정보) 임시 저장</li>
<li>트랙잭션/커넥션 관리</li>
</ol>
<hr />
<h2 id="✅스레드-로컬을-적용해서-로그-추적을-해보자">✅스레드 로컬을 적용해서 로그 추적을 해보자</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/477f1cf2-6c19-46e1-b7b9-d23835450d0c/image.png" /></p>
<blockquote>
<p>이전에 만들었던 로그 추적기에 스레드 로컬을 적용해서 각 로그마다 고유한 값인 traceId를 주어서 로그 추적을 해보자.
traceId를 사용하면 동시성 문제 없이 로그 흐름을 추적 가능하다는 장점이 있다.</p>
</blockquote>
<h3 id="1-traceidholder-생성">1. TraceIdHolder 생성</h3>
<pre><code class="language-java">// 이 클래스는 스레드마다 고유한 요청 ID를 저장하고 꺼내는 역할을 한다.
public class TraceIdHolder {
    //Traceid : 하나의 요청을 고유하게 식별하는 ID(쓰레드로컬)
    private static final ThreadLocal&lt;String&gt; threadLocal = new ThreadLocal&lt;&gt;();

    //ThradLocal에 값을 저장하고 꺼낼 set메서드와 get메서드
    public static void set(String traceId){
        threadLocal.set(traceId);
    }

    public static String get(){
        return threadLocal.get();
    }

    //하나의 요청이 끝났을 때 ThradLocal에 저장된 값을 지우기 위한 clear 메서드
    public static void clear(){
        threadLocal.remove();
    }
}</code></pre>
<ul>
<li>요청이 끝나면 <strong>remove()를 호출</strong>해서 반드시 <strong>스레드 종료 전 메모리 누수 방지가 필요</strong>하다.</li>
</ul>
<hr />
<h3 id="2-jwttokenfilter에-스레드-로컬-적용">2. jwtTokenFilter에 스레드 로컬 적용</h3>
<pre><code class="language-java">@Slf4j
@Component
@RequiredArgsConstructor
public class JwtTokenFilter extends OncePerRequestFilter {

 /* 생략 */

    // HTTP 매 요청마다 호출
    @Override
    protected void doFilterInternal(HttpServletRequest request, //http 요청
                                    HttpServletResponse response, //http 응답
                                    FilterChain filterChain
    ) throws ServletException, IOException {

        try{
            //HTTP요청이 실질적으로 시작되는 곳은 이곳(JwtTokenFilter) 이므로 여기서 TraceID 발급
            //TraceId 발급
            String traceId = UUID.randomUUID().toString().substring(0,8);
            TraceIdHolder.set(traceId);

            String accessToken = getTokenFromRequest(request); //요청 헤더에서 토큰 추출

            //토큰이 존재하고 유효한 경우
            if (accessToken != null &amp;&amp; jwtTokenProvider.validateToken(accessToken)) {
                UsernamePasswordAuthenticationToken authenticationToken =getAuthentication(accessToken);
                //토큰에서 사용자를 꺼내서 담은 사용자 인증 객체
                authenticationToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                //http요청으로부터 부가 정보(ip,세션 등)를 추출해서 사용자 인증 객체에 넣어줌
                SecurityContextHolder.getContext().setAuthentication(authenticationToken);
                //토큰에서 사용자 인증정보를 조회해서 인증정보를  현재 스레드에 인증된 사용자로 등록

                String url = request.getRequestURI().toString();
                String method = request.getMethod(); // &quot;GET, POST, PUT&quot;

                log.info(&quot;현재 들어온 HTTP 요청 = &quot; + url);
                log.info(&quot;✅ 토큰 인증 성공: &quot; + accessToken);
            }
            else {
                log.info(&quot;❌ 토큰 없음 또는 유효하지 않음: &quot; + accessToken);
            }
            filterChain.doFilter(request,response); // JwtTokenFilter를 거치고 다음 필터로 넘어감

        }finally {
            //Http 요청이 끝날 때 데이터를 비워줌
            TraceIdHolder.clear();
            String afterClear = TraceIdHolder.get();
            log.info(&quot;TraceHolder 데이터 확인 : {}&quot;, afterClear);    //null;
        }
    }
}</code></pre>
<ul>
<li>H<strong>TTP 요청이 실질적으로 시작 되는 곳은 JwtTokenFilter</strong> 이므로 이곳에서 traceId를 발급한다.</li>
<li>UUID를 사용해 고유한 문자열을 만들고, 그 앞의 8자리만 잘라서 traceId로 지정한다.</li>
<li>또한, HTTP 요청이 끝나면 ThreadLocal 데이터를 비우기 위해 <code>clear()</code>를 실행해준다.</li>
</ul>
<hr />
<h3 id="3-logaspectjava">3. LogAspect.java</h3>
<pre><code class="language-java">@Slf4j
@Component
@Aspect //공통으로 관리하고 싶은 기능을 담당하는 클래스에 붙이는 어노테이션
public class LogAspect {         
    //PointCut
    //AOP를 적용할 클래스
    @Pointcut(&quot;execution(* org.example.backendproject.board.service..*(..)) ||&quot; +
            &quot;execution(* org.example.backendproject.Auth.service..*(..)) ||&quot; +
            &quot;execution(* org.example.backendproject.user.service..*(..))&quot; )
    public void method(){}

    //@Aroun는 호출 시작과 종료 모두에 관리할 수 있는 AOP Advice
   @Around(&quot;method()&quot;)
    public Object around(ProceedingJoinPoint joinPoint) throws Throwable{


       long start = System.currentTimeMillis();
        String methodName = joinPoint.getSignature().getName(); //aop가 실행된 메서드
       try{
           log.info(&quot;[AOP_LOG][TraceId]{} {} 메서드 호출 시작 &quot;,TraceIdHolder.get(), methodName);

           Object result = joinPoint.proceed(); //joinpoint //aop를 적용할 시점
           return result;
       }
       catch (Exception e){
           log.error(&quot;[AOP_LOG][TraceId]{} {} 메서드 예외 {}  &quot;,TraceIdHolder.get(), methodName, e.getMessage());
           return e;
       }finally {
           long end = System.currentTimeMillis();
           log.info(&quot;[AOP_LOG][TraceId]{} {} 메서드 실행 완료 시간 = {} &quot;,TraceIdHolder.get(), methodName, end - start);
       }
   }
}</code></pre>
<ul>
<li>이전 AOP 실습 시 만들어 둔 AOP 관련 클래스다.</li>
<li>이 클래스에서 매번 메서드 관련 로그를 찍도록 로직을 짰는데, 로그에 TraceId를 추가한다.</li>
</ul>
<hr />
<h3 id="4-로그-확인">4. 로그 확인</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/86ded150-a5a2-4eb0-ab70-594c25d4fe07/image.png" /></p>
<ul>
<li>로그에 해당 HTTP 요청의 스레드 로컬인 TraceId가 출력되는 것을 확인할 수 있다.</li>
</ul>
<hr />
<h3 id="🌱참고--logstash에서-로그-식별-로직-추가하기">🌱참고 : logstash에서 로그 식별 로직 추가하기</h3>
<pre><code class="language-bash"> if &quot;AOP_LOG&quot; in [message] {
     mutate {
       add_field =&gt; { &quot;log_type&quot; =&gt; &quot;aop&quot; }
     }
   }
   else if &quot;OAuth2_LOG&quot; in [message]{
     mutate {
       add_field =&gt; { &quot;log_type&quot; =&gt; &quot;OAuth2&quot; }
     }
   }
   else if &quot;TraceId&quot; in [message] {
    mutate{
        add_field =&gt; {&quot;log_type&quot; =&gt; &quot;TraceId&quot;}
    }
   }</code></pre>
<ul>
<li>logstash.conf 파일에 위와 같이 TraceId를 식별하는 로그 식별 로직을 적어주면 키바나에서 검색도 가능!</li>
</ul>