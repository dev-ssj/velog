<blockquote>
<p>개발 시, 공통적으로 처리해야 하는 로직들이 생길 때가 많다.
이럴 때마다 공통 업무에 관련된 코드들을 페이지 마다 작성한다면 중복 코드가 많아지고, 서버에 부하를 줄 수도 있다.
이런 문제를 처리하기 위해 Spring에서는 공통적으로 여러 작업을 처리함으로써 중복 코드를 제거 할 수 있는 기능들인 <strong>필터</strong>와 <strong>인터셉터</strong>를 지원하고 있다.
필터와 인터셉터 모두 <strong>공통 관심사를 처리</strong>할 때 사용되는 기능들이다.</p>
</blockquote>
<hr />
<h1 id="💡필터filter">💡필터(Filter)</h1>
<h2 id="✅필터란">✅필터란?</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/fb74fc79-2cfb-4e3c-8881-ed8af381598e/image.png" /></p>
<p>필터는 <strong>Servlet 스펙</strong>에 정의된 기능으로, 요청과 응답을 거른 뒤 정제하는 역할을 하며, <strong>Dispatcher Servlet보다 먼저 동작하는 레벨</strong>에서 요청을 처리한다.
즉, 사용자의 요청이 Spring MVC의 핵심 컨트롤러인 <strong><span style="color: red;">DispatcherServlet에 도달하기 전에 먼저 거치게 되는 중간 단계</strong></span>라고 할 수 있다.
필터는 자바 웹 애플리케이션이 작동하는 가장 바깥 영역에서 동작하기 때문에, 정적 자원을 포함한 <strong>모든 HTTP 요청에 대해 적용</strong>된다.
또한, 스프링 컨테이너가 아닌 톰캣과 같은 웹 컨테이너에 의해 관리가 되며, <strong>스프링 범위 밖에서 처리</strong>된다.</p>
<hr />
<h2 id="✅필터의-주요-메서드">✅필터의 주요 메서드</h2>
<p>필터를 사용하기 위해서는 <code>javax.servlet.Filter</code> 인터페이스를 구현해야 하며, 다음과 같은 메소드를 가진다.</p>
<pre><code class="language-java">public interface Filter {     
public default void init(FilterConfig filterConfig) throws ServletException {}     
public void doFilter(ServletRequest request, ServletResponse response, 
                        FilterChain chain) throws IOException, ServletException;     
public default void destroy() {}
</code></pre>
<hr />
<h3 id="1-init">1. init()</h3>
<ul>
<li>필터 인스턴스가 <strong>최초 생성될 때 한번 호출</strong>되며, <strong>필터 객체를 초기화</strong>하고 서비스에 추가하기 위한 메서드이다.</li>
<li>웹 컨테이너가 1회 init()을 호출하여 필터 객체를 초기화하면 이후 요청들은 doFilter()를 통해 처리된다.<h4 id="📍사용-예시">📍사용 예시</h4>
<pre><code class="language-java">@Override
public void init(FilterConfig filterConfig) throws ServletException {
  System.out.println(&quot;필터 초기화&quot;);
}</code></pre>
</li>
</ul>
<hr />
<h3 id="2-dofilter">2. doFilter()</h3>
<ul>
<li><p>클라이언트로부터** 요청이 올때 마다 매번 호출**되며, HTTP 요청이 디스패처 서블릿으로 전달되기 전에 웹 컨테이너에 의해 실행되는 메서드이다.</p>
</li>
<li><p>doFilter의 파라미터로 가지는 FilterChain을 통해 다음 대상으로 요청을 전달 할 수 있게 된다.</p>
</li>
<li><p><code>chain.doFilter()</code>로 전, 후에 필요한 처리 과정을 넣어서 원하는 처리를 진행할 수 있다.</p>
<h4 id="📍사용-예시-1">📍사용 예시</h4>
<pre><code class="language-java">@Override
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
      throws IOException, ServletException {

  // 요청 전 처리
  System.out.println(&quot;요청 전 필터 처리&quot;);

  chain.doFilter(request, response); // 다음 필터 또는 서블릿 호출

  // 응답 후 처리
  System.out.println(&quot;응답 후 필터 처리&quot;);
}</code></pre>
</li>
</ul>
<hr />
<h3 id="3-destroy">3. destroy()</h3>
<ul>
<li><strong>서버 종료 또는 필터 제거 시 한번만 호출</strong>되며, 필터 객체를 제거하고 사용하는 자원을 반환하기 위한 메서드이다.</li>
<li>웹 컨테이너가 1회 destroy()를 호출하여 필터 객체를 종료하면 이후에는 doFilter에 의해 처리되지 않는다.<h4 id="📍사용-예시-2">📍사용 예시</h4>
<pre><code class="language-java">@Override
public void destroy() {
  System.out.println(&quot;필터 종료&quot;);
}</code></pre>
</li>
</ul>
<h2 id="✅필터의-사용-사례">✅필터의 사용 사례</h2>
<ol>
<li>보안 및 인증/인가 관련 작업</li>
<li>모든 요청에 대한 로깅 또는 검사</li>
<li>이미지/데이터 압축 및 문자열 인코딩</li>
<li>Spring과 분리되어야 하는 기능</li>
</ol>
<blockquote>
<p>➡️필터는 기본적으로 <strong>스프링과 무관하게 전역적으로 처리해야 하는 작업들을 처리</strong>할 수 있다.</p>
</blockquote>
<hr />
<h1 id="💡인터셉터interceptor">💡인터셉터(Interceptor)</h1>
<h2 id="✅인터셉터란">✅인터셉터란?</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/3171a6a3-c063-4908-bce0-d0d80961b017/image.png" />
인터셉터는 <strong>Spring MVC에서 제공하는 기능</strong>으로, DispatcherServlet이 요청을 받아 <span style="color: red;"><strong>Controller를 호출하기 전 / 후, 그리고 응답이 렌더링 되기 직전</strong></span>에 끼어들어 요청과 응답을 참조하거나 가공할 수 있는 기능을 제공한다.
웹 컨테이너에서 동작하는 필터와 달리 인터셉터는 <strong>스프링 컨텍스트에서 동작</strong>한다.
디스패처 서블릿이 핸들러매핑을 통해 컨트롤러를 찾도록 요청하는데, 그 결과로 실행 체인(HandlerExecutionChain)을 돌려준다.
여기에 인터셉터가 등록되어 있다면 순차적으로 인터셉터들을 거쳐 컨트롤러가 실행되도록 하고, 인터셉터가 없다면 바로 컨트롤러를 실행한다.</p>
<hr />
<h2 id="✅인터셉터의-주요-메서드">✅인터셉터의 주요 메서드</h2>
<p>인터셉터를 추가하기 위해서 <code>org.springframework.web.servlet</code>의 <code>HandlerInterceptor</code> 인터페이스를 구현해야하며, 다음과 같은 메서드를 가진다.</p>
<pre><code class="language-java">public interface HandlerInterceptor {
    boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
        throws Exception;

    void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
        ModelAndView modelAndView) throws Exception;

    void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
        Exception ex) throws Exception;
}</code></pre>
<hr />
<h3 id="1-prehandle">1. preHandle()</h3>
<ul>
<li><strong>컨트롤러 호출 직전에 실행</strong>되며, 컨트롤러 이전에 처리해야하는 전처리 작업이나 요청 정보를 가공하거나 추가하는 경우에 사용할 수 있다.<h4 id="📍사용-예시-3">📍사용 예시</h4>
<pre><code class="language-java">@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
      throws Exception {
  System.out.println(&quot;Interceptor - preHandle&quot;);
  return true; // false를 리턴하면 요청이 컨트롤러까지 가지 않음
}</code></pre>
</li>
</ul>
<hr />
<h3 id="2-posthandle">2. PostHandle()</h3>
<ul>
<li>** 컨트롤러가 정상적으로 실행 된 후, 뷰 렌더링 전에 호출**되며, 컨트롤러 이후에 처리해야하는 후처리 작업이 있을 때 사용할 수 있다.</li>
<li>postHandle()에는 컨트롤러가 반환하는<code>ModelAndView</code> 타입의 정보가 제공되는데, 최근에는 JSON 형태로 데이터를 제공하는 RestAPI기반의 컨트롤러(<code>@RestController)</code>를 만들면서 자주 사용되지 않는다.</li>
</ul>
<h4 id="📍사용-예시-4">📍사용 예시</h4>
<pre><code class="language-java">@Override
public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
                       ModelAndView modelAndView) throws Exception {
    System.out.println(&quot;Interceptor - postHandle&quot;);
}</code></pre>
<hr />
<h3 id="3-aftercompletion">3. afterCompletion()</h3>
<ul>
<li>뷰 렌더링 완료 후, <strong>모든 뷰에서 최종 결과를 생성하는 일을 포함해 모든 작업이 완료된 후에 실행</strong>된다.</li>
<li>요청 처리 중에 사용한 리소스를 반환할 때 사용할 수 있다.<h4 id="📍사용-예시-5">📍사용 예시</h4>
<pre><code class="language-java">@Override
public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
                          Exception ex) throws Exception {
  System.out.println(&quot;Interceptor - afterCompletion&quot;);
}</code></pre>
</li>
</ul>
<hr />
<h2 id="✅인터셉터의-사용-사례">✅인터셉터의 사용 사례</h2>
<ol>
<li>세부적인 보안 및 인증/인가 공통 작업</li>
<li>API 호출에 대한 로깅 또는 검사</li>
<li>컨트롤러로 넘겨주는 정보(데이터)의 가공</li>
</ol>
<blockquote>
<p>➡️인터셉터에서는 <strong>클라이언트의 요청과 관련되어 전역적으로 처리해야하는 작업</strong>들을 처리할 수 있다.</p>
</blockquote>
<hr />
<h2 id="☑️정리">☑️정리</h2>
<table>
<thead>
<tr>
<th>항목</th>
<th>Filter</th>
<th>Interceptor</th>
</tr>
</thead>
<tbody><tr>
<td><strong>기반 기술</strong></td>
<td>Servlet 스펙</td>
<td>Spring MVC</td>
</tr>
<tr>
<td><strong>관리되는 컨테이너</strong></td>
<td>웹 컨테이너</td>
<td>스프링 컨테이너</td>
</tr>
<tr>
<td><strong>동작 위치</strong></td>
<td>DispatcherServlet 이전</td>
<td>DispatcherServlet 이후</td>
</tr>
<tr>
<td>*<em>적용 대상 *</em></td>
<td>모든 요청 (정적 리소스 포함)</td>
<td>스프링 컨트롤러 요청만</td>
</tr>
<tr>
<td>*<em>용도 *</em></td>
<td>1. 공통된 보안 및 인증/인가 관련 작업<br />2. 모든 요청에 대한 로깅 또는 감사<br /> 3. 이미지/데이터 압축 및 문자열 코딩<br /> 4. Spring과 분리되어야 하는 기능</td>
<td>1. 세부적인 보안 및 인증/인가 공통 작업<br />2. API 호출에 대한 로깅 또는 감사<br />3. Controller로 넘겨주는 정보(데이터)의 가공</td>
</tr>
</tbody></table>
<blockquote>
<p>필터와 인터셉트 모두 <strong>비즈니스 로직과 분리되어 특정 요구사항(보안, 인증, 인코딩 등)을 만족시켜야할 때 적용</strong> 한다.</p>
</blockquote>
<blockquote>
<p><span style="color: red;"><strong>필터</span>는 특정 요청과 컨트롤러에 관계 없이 전역적으로 처리해야하는 작업이나 웹 어플리케이션에 전반적으로 사용되는 기능을 구현할 때 적용</strong>하고, 
<span style="color: blue;"><strong>인터셉터</span>는 클라이언트의 요청과 관련된 작업에 대해 추가적인 요구사항을 만족해야할 때 적용</strong>한다.</p>
</blockquote>