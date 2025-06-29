<h1 id="💡aopaspect-oriented-programming란">💡AOP(Aspect-Oriented Programming)란?</h1>
<blockquote>
<p>AOP(Aspect-Oriented Programming, 관점 지향 프로그래밍)는 <strong>공통 관심 사항(Cross-Cutting Concerns)</strong>을 모듈화하여 핵심 비즈니스 로직과 분리할 수 있게 해주는 프로그래밍 패러다임을 뜻한다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/6e336acb-ea82-43cb-b3db-3030ed08faf8/image.png" /></p>
</blockquote>
<hr />
<h2 id="🤔왜-aop가-필요한데">🤔왜 AOP가 필요한데?</h2>
<p>애플리케이션에서는 로깅, 트랜잭션 처리, 예외 처리등과 같은 공통 기능들이 자주 등장한다. 이러한 <strong>공통 기능들을 모든 서비스 로직 안에 반복해서 작성하면 코드 중복이 증가하고, 유지보수가 어려워지며 어떤게 핵심 로직인지 단번에 알기 어렵다.</strong>
예를 들어, 메서드 시작 시간과 메서드 종료 시간을 체크해서 걸린 시간을 출력하는 기능을 메서드에 넣으려면</p>
<pre><code class="language-java">     /** 글 등록 **/
    @Transactional
    public BoardDTO createBoard(BoardDTO boardDTO) {
        long start = System.currentTimeMillis();    //메서드 시작 시간
        System.out.println(&quot;글 작성 메서드 시작&quot;);

        // userId(PK)를 이용해서 User 조회
        if (boardDTO.getUser_id() == null)
            throw new IllegalArgumentException(&quot;userId(PK)가 필요합니다!&quot;);

        User user = userRepository.findById(boardDTO.getUser_id())
                .orElseThrow(() -&gt; new IllegalArgumentException(&quot;작성자 정보가 올바르지 않습니다.&quot;));

        Board board = new Board();
        board.setTitle(boardDTO.getTitle());
        board.setContent(boardDTO.getContent());

        board.setUser(user);
        Board saved = boardRepository.save(board);

        long end = System.currentTimeMillis();
        log.info(&quot;글 작성 완료 시간 = &quot; + (end-start));

        return toDTO(saved);
    }

        /** 게시글 수정 **/
    @Transactional
    public BoardDTO updateBoard(Long boardId, BoardDTO dto) {

        long start = System.currentTimeMillis();    //메서드 시작 시간
        System.out.println(&quot;글 수정 메서드 시작&quot;);
        Board board = boardRepository.findById(boardId)
                .orElseThrow(() -&gt; new IllegalArgumentException(&quot;게시글 없음: &quot; + boardId));
        board.setTitle(dto.getTitle());
        board.setContent(dto.getContent());
        boardRepository.save(board);

        long end = System.currentTimeMillis();
        log.info(&quot;글 수정 완료 시간 = &quot; + (end-start));

        return toDTO(board);
    }</code></pre>
<p>위의 코드와 같이 모든 메서드에 메서드 시작 시간과 종료시간에 관련된 로직을 작성해야한다. 지금은 2개의 메서드에만 적용했지만, 이런 메서드가 수백, 수천개라면 어떻게 할 것인가? 수백 수천개의 메서드에 저 코드들을 다 넣는다고 생각하면 눈앞이 깜깜해진다.
<strong>이럴 때 쓰는것이 AOP이다.</strong>
AOP를 적용하면 아래 코드와 같이 공통 기능이 담긴 클래스를 작성한 후 AOP 관련 어노테이션과 함께 공통 기능 로직을 작성해주면 <strong>공통 기능을 따로 메서드 안에 작성하지 않아도 공통 기능 로직이 처리가 된다.</strong></p>
<pre><code class="language-java">@Slf4j
@Component
@Aspect //공통으로 관리하고 싶은 기능을 담당하는 클래스에 붙이는 어노테이션
public class LogAspect {
    @Pointcut(&quot;execution(* org.example.backendproject.board.service..*(..)) || execution(* org.example.backendproject.Auth.service..*(..))&quot;)
    public void method(){}


    //AOP를 적용할 클래스
    @Around(&quot;execution(* org.example.backendproject.board.service..*(..)) || execution(* org.example.backendproject.Auth.service..*(..))&quot;)
    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        String methodName = joinPoint.getSignature().getName();

        try {
            log.info(&quot;[AOP_LOG] {} 메서드 호출 시작 &quot;, methodName);
            Object result = joinPoint.proceed();
            return result;
        } catch (Exception e) {
            log.error(&quot;[AOP_LOG] {} 메서드 예외 {}&quot;, methodName, e.getMessage());
            return e;
        } finally {
            long end = System.currentTimeMillis();
            log.info(&quot;[AOP_LOG] {} 메서드 실행 완료 시간 = {}&quot;, methodName, end - start);
        }
    }
}</code></pre>
<blockquote>
<p>이제부터 이런 문제점을 해결해주는 AOP에 대해 자세히 알아보도록 하자.</p>
</blockquote>
<hr />
<h2 id="✅aop의-핵심-구성-요소">✅AOP의 핵심 구성 요소</h2>
<p>AOP의 핵심 구성 요소는 아래와 같이 총 다섯가지로 나뉘어진다.</p>
<table>
<thead>
<tr>
<th>구성 요소</th>
<th>설명</th>
<th>예시</th>
</tr>
</thead>
<tbody><tr>
<td><code>@Aspect</code></td>
<td>이 클래스가 Aspect임을 정의</td>
<td><code>@Aspect public class LoggingAspect { ... }</code></td>
</tr>
<tr>
<td><code>Advice</code></td>
<td>공통 기능(로직)이 실행되는 시점</td>
<td><code>@Before</code>, <code>@After</code>, <code>@Around</code>, <code>@AfterThrowing</code>, <code>@AfterReturning</code></td>
</tr>
<tr>
<td><code>JoinPoint</code></td>
<td>Advice가 적용 가능한 지점 (메소드 실행 등)</td>
<td>메소드 실행 전/후</td>
</tr>
<tr>
<td><code>Pointcut</code></td>
<td>JoinPoint를 선별하는 조건</td>
<td><code>execution(* com.example.service..*(..))</code></td>
</tr>
<tr>
<td><code>Weaving</code></td>
<td>Aspect를 실제 객체에 적용하는 작업</td>
<td>Spring은 런타임에 프록시 방식으로 적용</td>
</tr>
</tbody></table>
<hr />
<h3 id="1️⃣aspect-관점">1️⃣Aspect (관점)</h3>
<h4 id="📌정의">📌정의</h4>
<blockquote>
<p><strong>공통 기능(로깅, 보안 검사, 트랙잭션 등)을 모듈화한 클래스</strong>를 의미. <code>@Aspect</code> 어노테이션을 사용한다.</p>
</blockquote>
<h4 id="☑️예시">☑️예시</h4>
<pre><code class="language-java">@Aspect    //공통 기능을 정의하는 클래스임을 알려줌
@Component
public class LoggingAspect {
    // 공통 기능들 정의됨
}</code></pre>
<hr />
<h3 id="2️⃣advice-공통-기능-로직">2️⃣Advice (공통 기능 로직)</h3>
<h4 id="📌정의-1">📌정의</h4>
<blockquote>
<p><strong>Aspect 안에 있는 실제 로직</strong>. 언제, 어떤 방식으로 실행할 지 정의함</p>
</blockquote>
<h4 id="🔧관련-어노테이션">🔧관련 어노테이션</h4>
<table>
<thead>
<tr>
<th>종류</th>
<th>설명</th>
<th>사용 예시</th>
</tr>
</thead>
<tbody><tr>
<td><code>@Before</code></td>
<td>메서드 실행 <strong>전</strong> 수행</td>
<td>로그 출력, 인증 검사</td>
</tr>
<tr>
<td><code>@AfterReturning</code></td>
<td>메서드 <strong>성공적으로 리턴 후</strong> 수행</td>
<td>리턴값 로그</td>
</tr>
<tr>
<td><code>@AfterThrowing</code></td>
<td>메서드 <strong>예외 발생 시</strong> 수행</td>
<td>예외 로깅</td>
</tr>
<tr>
<td><code>@After</code></td>
<td>메서드 종료 후 (정상/예외 관계 없이)</td>
<td>리소스 정리</td>
</tr>
<tr>
<td><code>@Around</code></td>
<td>실행 전후를 모두 감쌈</td>
<td>성능 측정, 커스텀 인증, 트랜잭션</td>
</tr>
</tbody></table>
<h4 id="☑️예시-1">☑️예시</h4>
<pre><code class="language-java">@Before(&quot;execution(* com.example.service..*(..))&quot;)
public void logBefore(JoinPoint joinPoint) {
    System.out.println(&quot;▶️ &quot; + joinPoint.getSignature());
}</code></pre>
<ul>
<li><strong>@Before(...)</strong> : 타겟 메서드가 실행되기 바로 전에 실행되는 것을 의미</li>
<li><strong>&quot;execution(* com.example.service..*(..))&quot;</strong> : 포인트컷 표현식. 
어떤 메서드에 적용할지 범위를 설정하는 것. 
➡️<strong>com.example.service 및 그 하위 패키지의 모든 클래스의 모든 메서드 실행 전에 logBefore()가 실행된다는 의미</strong></li>
</ul>
<hr />
<h3 id="3️⃣joinpoint-실행-지점-정보">3️⃣JoinPoint (실행 지점 정보)</h3>
<h4 id="📌정의-2">📌정의</h4>
<blockquote>
<p><strong>Advice가 적용될 수 있는 지점 중 하나</strong>로, Spring AOP는 기본적으로 메서드 실행만 지원한다.</p>
</blockquote>
<h4 id="🔍용도">🔍용도</h4>
<ul>
<li>어떤 메서드가 실행됐는지 (<code>getSignature()</code>)</li>
<li>어떤 인자가 들어왔는지 (<code>getArgs()</code>)</li>
<li>실제 호출 객체는 뭔지 (<code>getTarget()</code>)</li>
</ul>
<h4 id="☑️예시-2">☑️예시</h4>
<pre><code class="language-java">public void logBefore(JoinPoint joinPoint) {
    System.out.println(&quot;메서드 이름: &quot; + joinPoint.getSignature().getName());
    System.out.println(&quot;인자들: &quot; + Arrays.toString(joinPoint.getArgs()));
}</code></pre>
<hr />
<h3 id="4️⃣pointcut-적용-대상-필터">4️⃣Pointcut (적용 대상 필터)</h3>
<h4 id="📌정의-3">📌정의</h4>
<blockquote>
<p><strong>Advice를 어떤 메서드에 적용할 지 지정하는 표현식</strong> -&gt; 즉, 어디에 적용할 것인가를 필터링 하는 역할</p>
</blockquote>
<h4 id="☑️예시-3">☑️예시</h4>
<pre><code class="language-java">// com.example.service 패키지 이하 모든 클래스의 모든 메서드
@Pointcut(&quot;execution(* com.example.service..*(..))&quot;)</code></pre>
<table>
<thead>
<tr>
<th>표현식</th>
<th>의미</th>
</tr>
</thead>
<tbody><tr>
<td><code>*</code></td>
<td>모든 반환 타입</td>
</tr>
<tr>
<td><code>..</code></td>
<td>하위 패키지 또는 모든 파라미터</td>
</tr>
<tr>
<td><code>execution(...)</code></td>
<td>포인트컷 지정 문법</td>
</tr>
</tbody></table>
<hr />
<h3 id="5️⃣weaving-위빙">5️⃣Weaving (위빙)</h3>
<h4 id="📌정의-4">📌정의</h4>
<blockquote>
<p>** Aspect를 어디에, 언제, 어떻게 적용할지 결정하고 실제로 반영하는 작업** -&gt;Spring에서는 런타임에 프록시 객체를 만들어서 위빙을 수행함</p>
</blockquote>
<h4 id="🧠방식">🧠방식</h4>
<table>
<thead>
<tr>
<th>방식</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><strong>프록시 기반 (Spring AOP)</strong></td>
<td>클래스 또는 인터페이스를 감싼 프록시를 런타임에 생성</td>
</tr>
<tr>
<td><strong>바이트코드 조작 (AspectJ)</strong></td>
<td>컴파일 타임 또는 로드 타임에 바이트코드 변경</td>
</tr>
</tbody></table>
<hr />
<h2 id="✅aop-적용해보기">✅AOP 적용해보기</h2>
<blockquote>
<p>com.example.service 패키지 아래의 모든 서비스 메소드 실행 전/후에 로그를 남기고 싶다면?</p>
</blockquote>
<h3 id="📍aspect-클래스-작성">📍Aspect 클래스 작성</h3>
<pre><code class="language-java">@Slf4j    
@Aspect        //공통 로직을 가진 클래스임을 명시
@Component
public class LoggingAspect {

    // 포인트컷: 서비스 패키지 내 모든 메서드 지정
    @Pointcut(&quot;execution(* com.example.service..*(..))&quot;)
    public void serviceLayer() {}

    // 메서드 실행 전 Advice
    @Before(&quot;serviceLayer()&quot;)
    public void logBefore(JoinPoint joinPoint) {
        log.info(&quot;▶️ 메서드 시작: {}&quot;, joinPoint.getSignature());
    }

    // 메서드 정상 종료 후 Advice
    @AfterReturning(pointcut = &quot;serviceLayer()&quot;, returning = &quot;result&quot;)
    public void logAfter(JoinPoint joinPoint, Object result) {
        log.info(&quot;✅ 메서드 종료: {}, 반환값: {}&quot;, joinPoint.getSignature(), result);
    }

    // 예외 발생 시 Advice
    @AfterThrowing(pointcut = &quot;serviceLayer()&quot;, throwing = &quot;ex&quot;)
    public void logException(JoinPoint joinPoint, Exception ex) {
        log.error(&quot;❌ 예외 발생: {}, 메시지: {}&quot;, joinPoint.getSignature(), ex.getMessage());
    }
}</code></pre>
<h3 id="📍실행-결과-예시">📍실행 결과 예시</h3>
<pre><code class="language-java">▶️ 메서드 시작: String com.example.service.MemberService.getMemberName(Long)
✅ 메서드 종료: String com.example.service.MemberService.getMemberName(Long), 반환값: 사용자_1

-- 또는 예외 발생 시 --
▶️ 메서드 시작: String com.example.service.MemberService.getMemberName(Long)
❌ 예외 발생: String com.example.service.MemberService.getMemberName(Long), 메시지: ID는 null일 수 없습니다.</code></pre>
<h3 id="📍around를-이용해-메서드-실행-전후를-모두-감싸려면">📍@Around를 이용해 메서드 실행 전후를 모두 감싸려면?</h3>
<pre><code class="language-java">@Slf4j
@Component
@Aspect
public class LogAspect {
    @Pointcut(&quot;execution(* org.example.backendproject.board.service..*(..))


    //AOP를 적용할 클래스
    @Around(&quot;execution(* org.example.backendproject.board.service..*(..)) || execution(* org.example.backendproject.Auth.service..*(..))&quot;)
    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        String methodName = joinPoint.getSignature().getName();

        try {
            log.info(&quot;[AOP_LOG] {} 메서드 호출 시작 &quot;, methodName);
            Object result = joinPoint.proceed();
            return result;
        } catch (Exception e) {
            log.error(&quot;[AOP_LOG] {} 메서드 예외 {}&quot;, methodName, e.getMessage());
            return e;
        } finally {
            long end = System.currentTimeMillis();
            log.info(&quot;[AOP_LOG] {} 메서드 실행 완료 시간 = {}&quot;, methodName, end - start);
        }
    }
}</code></pre>