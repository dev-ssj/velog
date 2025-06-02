<h2 id="💡스프링의-3대-요소란">💡스프링의 3대 요소란?</h2>
<p>Spring Framework의 주축이 되는 3가지 개념을 뜻한다.</p>
<ul>
<li>IoC(Inversion of Control, 제어의 역전)</li>
<li>AOP(Aspect Oriented Programming, 관점 지향 프로그래밍)</li>
<li>PSA(Portable Service Abstraction, 서비스 추상화)
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/dfcd39d4-f654-4b21-9bbc-608ea2d8c0f1/image.png" /></li>
</ul>
<hr />
<h2 id="1-✅iocinversion-of-control-제어의-역전">1. ✅IoC(Inversion of Control, 제어의 역전)</h2>
<blockquote>
<p>IoC는 객체의 생성부터 생명주기 관리까지 모든 객체에 대한 제어권을 개발자가 아닌 <code>스프링 컨테이너</code>가 대신 관리하는 것을 말한다.
컴포넌트 의존 관계 설정, 설정, 생명주기를 해결하기 위한 디자인 패턴이다.
➡️객체를 개발자가 <code>new</code>로 직접 만들지 않고 스프링이 알아서 만들어주고 주입해준다.</p>
</blockquote>
<hr />
<h3 id="📌스프링-컨테이너가-뭔데">📌스프링 컨테이너가 뭔데?</h3>
<p>스프링 컨테이너(IoC 컨테이너)는 객체(Bean)를 생성하고 의존성을 주입하며 생명주기를 관리해주는 핵심 컴포넌트이다.
 <img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/77d97093-d605-4aa6-85e6-23b7e3b8fcfa/image.png" /></p>
<h3 id="❓왜-쓰기-시작했어">❓왜 쓰기 시작했어?</h3>
<ul>
<li>일반적인 객체 생성방법은 <code>new</code>를 사용해서 객체를 생성하는 것이다.<pre><code class="language-java">UserService userService = new UserService(new UserRepository());</code></pre>
<ul>
<li>하지만 이 방식은 클래스 간의 결합도가 높고, 테스트나 유지보수가 어렵다.</li>
</ul>
</li>
<li>그래서 등장한게 <strong>스프링 컨테이너이다.</strong></li>
<li>위 코드를 IoC 구현 방식 중 하나인 DI로 설계한다면 스프링이 필요한 의존 객체를 자동으로 주입해준다.</li>
</ul>
<pre><code class="language-java">public class UserService {
    private final UserRepository userRepository;

    @Autowired    //객체를 알아서 주입해주는 애노테이션 즉, UserService를 주입받고있다.
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;    //의존성 주입
    }
}</code></pre>
<h3 id="📌그럼-스프링-컨테이너가-하는-일은-뭐야">📌그럼 스프링 컨테이너가 하는 일은 뭐야?</h3>
<p><strong>1. 빈 객체 생성</strong></p>
<ul>
<li><code>@Component</code>, <code>@Service</code>, <code>@Repository</code>, <code>@Controller</code> 등을 스캔해서 객체를 생성</li>
</ul>
<p><strong>2. 의존성 주입(DI)</strong></p>
<ul>
<li>필요한 객체를 주입해줌 (<code>@Autowired</code>, <code>@Inject</code> ,생성자 주입 등)</li>
</ul>
<p><strong>3. 라이프 사이클 관리</strong></p>
<ul>
<li>객체 생성 → 초기화 → 소멸 등 생명주기 관리</li>
</ul>
<p><strong>4. AOP 적용</strong></p>
<ul>
<li>Aspect를 설정하여 메서드 실행 전/후에 공통 로직 실행</li>
</ul>
<h3 id="📌대표적인-컨테이너-종류">📌대표적인 컨테이너 종류</h3>
<table>
<thead>
<tr>
<th>컨테이너 이름</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><code>BeanFactory</code></td>
<td>단순한컨테이너에서 객체를 생성하고 DI를 처리하는 기능만 한다. <br />BeanFactory에 여러가지 기능을 추가한 것이 <code>ApplicationContext</code></td>
</tr>
<tr>
<td><code>ApplicationContext</code></td>
<td>가장 많이 사용하는 스프링 컨테이너. 다양한 기능 제공</td>
</tr>
<tr>
<td><code>AnnotationConfigApplicationContext</code></td>
<td>자바 기반 설정(<code>@Configuration</code>)에서 사용하는 컨테이너</td>
</tr>
<tr>
<td><code>WebApplicationContext</code></td>
<td>웹 어플리케이션용 컨테이너. DispatcherServlet이 사용함</td>
</tr>
</tbody></table>
<hr />
<h2 id="📗didependency-injection-의존성-주입">📗DI(Dependency Injection, 의존성 주입)</h2>
<ul>
<li><p><code>의존성 주입</code>이란 객체 스스로가 의존 객체를 생성하지 않고, 외부(스프링 컨테이너)가 대신 만들어 넣어주는 방식을 뜻한다.</p>
<blockquote>
<p><strong>DI 작동 방식</strong></p>
</blockquote>
<ol>
<li>스프링이 <code>@Service</code>, <code>@Repository</code>, <code>@Component</code> 등으로 빈 등록</li>
<li><code>@Autowired</code>로 의존성을 요구한 클래스 발견</li>
<li>스프링이 해당 빈을 찾아서 생성자/필드/세터를 통해 주입</li>
</ol>
<hr />
</li>
</ul>
<h3 id="📌빈bean">📌빈(Bean)</h3>
<ul>
<li>빈이란 스프링 컨테이너가 관리하는 객체이다.</li>
<li>빈으로 만든 객체들을 스프링이 찾아서 의존성 주입을 하는 것.</li>
<li>주로 <code>@Component</code>와 <code>@Bean</code>을 사용한다.</li>
</ul>
<h4 id="component">@Component</h4>
<ul>
<li><strong>가장 많이 쓰는 방식</strong>으로, 스프링이 애노테이션을 자동으로 감지해 빈으로 등록한다.</li>
<li><code>@Contoller</code>, <code>@Service</code>, <code>@Repository</code> 모두 <code>@Component</code>를 포함하고 있다. 즉, 위 애너테이션을 사용한 클래스들은 모두 <strong>자동으로 Bean으로 등록</strong>된다.</li>
</ul>
<pre><code class="language-java">@Component
public class UserService {

    @Autowired
    private UserRepository userRepository;
}</code></pre>
<h4 id="bean">@Bean</h4>
<ul>
<li>좀 더 명시적으로 빈을 등록하고 싶을 때 사용한다.</li>
<li>특히, <strong>외부 라이브러리 객체</strong>를 빈으로 등록할 때 자주 사용 된다.</li>
</ul>
<pre><code class="language-java">@Configuration
public class AppConfig {

    @Bean
    public MyComponent myComponent() {
        return new MyComponent();
    }
}</code></pre>
<hr />
<h3 id="📌autowired">📌@Autowired</h3>
<ul>
<li>스프링이 <strong>의존성 주입(DI)</strong>을 자동으로 수행하기 위해 사용하는 애노테이션</li>
<li>스프링 컨테이너에 등록된 빈 중에서 <strong>필요한 타입의 객체를 자동으로 찾아 주입</strong>해주는 역할을 한다.</li>
<li>주입 방법은 <code>생성자 주입</code>, <code>필드 주입</code>, <code>세터 주입</code> 세가지가 있다.</li>
</ul>
<h4 id="1-생성자-주입constructor-injection--span-stylecolor-red🔥가장-권장되는-방식span">1. 생성자 주입(Constructor Injection)  <strong><span style="color: red;">🔥가장 권장되는 방식</span></strong></h4>
<pre><code class="language-java">@Service
public class UserService {
    private final UserRepository userRepository;

    @Autowired  // 생성자가 하나면 생략 가능
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}</code></pre>
<blockquote>
<p><strong>📌특징</strong></p>
</blockquote>
<ul>
<li>final로 선언 가능 → 불변성 유지</li>
<li>테스트 시 주입 쉬움</li>
<li>의존성이 누락되면 컴파일 또는 런타임 오류 발생 → 안전</li>
</ul>
<hr />
<h4 id="2-필드-주입feild-injection-❌지양하는-방식">2. 필드 주입(Feild Injection) ❌지양하는 방식</h4>
<pre><code class="language-java">@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;
}</code></pre>
<blockquote>
<p> <strong>📌특징</strong>
코드가 간결해 보임
하지만 private이라 테스트에서 mock 주입이 불가능
의존 관계를 명확히 알기 어려움 (생성자에 없음)
<strong>❌ 테스트 어려움 + 유지보수 불편 → 지양하는 방식</strong></p>
</blockquote>
<hr />
<h4 id="3-세터-주입setter-injection">3. 세터 주입(Setter Injection)</h4>
<pre><code class="language-java">@Service
public class UserService {
    private UserRepository userRepository;

    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}</code></pre>
<blockquote>
<p><strong>📌특징</strong>
선택적인 의존성 주입에 적합 (nullable 가능)
런타임 중에도 변경 가능 → 불변성 유지 안 됨
테스트는 가능하지만, 강제성 없음</p>
</blockquote>
<hr />
<h2 id="2-✅-aopaspect-oriented-programming-관점-지향-프로그래밍">2. ✅ AOP(Aspect Oriented Programming, 관점 지향 프로그래밍)</h2>
<blockquote>
<p><code>AOP(Aspect Oriented Programming, 관점 지향 프로그래밍)</code>는 공통적으로 반복되는 기능(관심사, Concern)을 <strong>핵심 로직과 분리해서 모듈화 할 수 있도록 해주는 프로그래밍 패러다임</strong>이다.
AOP를 사용하면 이 코드를 한 곳에 모아서 관리할 수 있고, 핵심 비즈니스 로직과 분리되어 유지보수성이 높아진다.</p>
</blockquote>
<hr />
<h3 id="📗aop의-핵심-용어">📗AOP의 핵심 용어</h3>
<table>
<thead>
<tr>
<th>용어</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><strong>Aspect</strong></td>
<td>공통 기능을 모듈화한 클래스 (ex: 트랜잭션 관리, 로그 등)<br /> Adivce와 Pointcut을 함께 가지고 있다.</td>
</tr>
<tr>
<td><strong>Advice</strong></td>
<td>Aspect가 실제로 실행되는 공통 기능의 코드 (ex: 메서드 실행 전 로그 출력)</td>
</tr>
<tr>
<td><strong>Join Point</strong></td>
<td>Advice가 적용될 수 있는 지점 (ex: 메서드 호출 시점)</td>
</tr>
<tr>
<td><strong>Pointcut</strong></td>
<td>Advice를 적용할 Join Point를 선별하는 기능을 정의한 모듈</td>
</tr>
<tr>
<td><strong>Weaving</strong></td>
<td>Advice를 핵심 로직에 적용하는 과정 (컴파일 시 / 로드 시 / 런타임 시)</td>
</tr>
<tr>
<td><strong>Target</strong></td>
<td>Advice가 적용될 실제 객체 또는 클래스</td>
</tr>
<tr>
<td><strong>Proxy</strong></td>
<td>Target을 감싸서 Target에 대한 요청을 대신 받아주는 Wrapping Object</td>
</tr>
<tr>
<td><strong>Introduction</strong></td>
<td>타겟 클래스에 코드 변경 없이 신규 메소드나 멤버 변수를 추가하는 기능</td>
</tr>
</tbody></table>
<hr />
<h3 id="📌-aop의-흐름-예시">📌 AOP의 흐름 예시</h3>
<ul>
<li><p>서비스 로직 실행 전에 로그를 남기고 싶다면</p>
<pre><code class="language-java">@Aspect
@Component
public class LoggingAspect {

 @Before(&quot;execution(* com.example.service.*.*(..))&quot;)
 public void logBefore(JoinPoint joinPoint) {
     System.out.println(&quot;메서드 실행 전: &quot; + joinPoint.getSignature().getName());
 }
}</code></pre>
<ul>
<li><code>@Aspect</code> : 이 클래스가 Aspect임을 명시</li>
<li><code>@Before</code> : 대상 메서드 실행 전에 Advice 실행</li>
<li><code>execution(* com.example.service.*.*(..))</code>: <code>Pointcut</code> 표현식</li>
</ul>
</li>
</ul>
<hr />
<h3 id="📗aop의-장점">📗AOP의 장점</h3>
<ul>
<li>관심사의 분리(SoC) → 핵심 로직과 공통 기능을 깔끔하게 분리</li>
<li>코드 중복 감소 </li>
<li>유지보수성과 가독성 향상</li>
<li>공통 정책을 한 곳에서 관리 가능 (ex: 전역 트랜잭션)</li>
</ul>
<hr />
<h2 id="3-✅-psaportable-service-abstraction-서비스-추상화">3. ✅ PSA(Portable Service Abstraction, 서비스 추상화)</h2>
<ul>
<li><code>PSA</code>란 환경의 변화와 관계없이 일관된 방식의 기술로의 접근 환경을 제공하는 추상화 구조를 말한다.</li>
<li>즉, 개발자가 특정 기술의 상세한 API에 의존하지 않고, Spring이 제공하는 <code>공통 API(인터페이스)</code>만 사용해서 <strong>코드의 이식성과 유지보수성을 높일 수 있도록 하는 개념</strong>이다.</li>
</ul>
<h3 id="📗psa의-대표적인-예시">📗PSA의 대표적인 예시</h3>
<h4 id="1-spring-web-mvc">1. Spring Web MVC</h4>
<blockquote>
<p>원래 Servlet을 사용하려면 HttpServlet을 상속받고 doGet(), doPost()등 오버라이딩하여 사용해야 한다.
@Contorller를 생각해보자. @Controller 어노테이션을 사용하면 요청을 매핑할 수 있는 컨트롤러 역할을 수행하는 클래스가 된다. 그 클래스에서는 @GetMapping과 @PostMapping 어느테이션을 사용해서 요청을 매핑할 수 있다.
이것이 &quot;잘 만든 인터페이스&quot;의 예시이며, 이러한 편의성 제공이 서비스 추상화의 목적이다.
@GetMapping같은 기능들의 뒷단의 복잡한 코드에 대해서 깊게 고려하지 않아도 되고 이런 기술들을 기반으로 하여 기존 코드를 거의 변경하지 않아도 된다.
또한, 실행 서버를 Tomcat이 아닌 netty로 변경하고 싶다면 프로젝트 설정에 spring-boot-starter-web 의존성 대신 spring-boot-starter-webflux 의존성을 받도록 하면 된다.
이렇게 Spring Web MVC는  사용자가 기존 코드를 거의 변경하지 않고, 웹 기술 스택을 간편하게 바꿀 수 있도록 해준다.</p>
</blockquote>
<h4 id="2-spring-transaction">2. Spring Transaction</h4>
<blockquote>
<p>Low level로 트랜잭션을 처리 하려면 명시적으로 setAutoCommit()과 commit(), rollback()등을 호출해야한다.
하지만 Spring이 제공하는 @Transaction 어노테이션을 사용하면 단순하게 메소드에 어노테이션을 붙여줌으로써 트랜잭션 처리가 간단하게 이루어진다.
또한, PSA로써 다양한 기술 스택으로 구현체를 바꿀 수있다.
즉, 기존 코드는 변경하지 않은 채로 트랜잭션을 실제로 처리하는 구현체를 사용 기술에 따라 바꿀 수 있는 것이다.</p>
</blockquote>