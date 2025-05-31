<h2 id="💡dispatcherservlet이란">💡DispatcherServlet이란?</h2>
<blockquote>
<p><code>DispatcherServlet</code>은 <code>javax.servlet.http.HttpServlet</code>을 상속한 클래스로서, Spring MVC의 핵심 프론트 컨트롤러 역할을 수행하는 클래스이다.
 클라이언트의 모든 요청을 중앙에서 받아 적절한 컨트롤러로 전달하고, 처리 결과를 View로 연결해주는 역할을 한다.
 <code>@SpringBootApplication</code>에 의해 자동으로 등록되며, 기본 URL패턴인 <code>/</code>에 매핑 된다.</p>
</blockquote>
<h2 id="💡dispatcherservlet의-주요-역할">💡DispatcherServlet의 주요 역할</h2>
<p>** 1. 요청 수신**</p>
<ul>
<li>클라이언트의 요청을 가장 먼저 받는다(<code>/</code>, <code>*.do</code> 등 매핑 가능)</li>
</ul>
<p>** 2. 핸들러 매핑**</p>
<ul>
<li><code>HandlerMapping</code>을 통해 어떤 컨트롤러(핸들러)가 이 요청을 처리할지 결정</li>
</ul>
<p>**  3. 핸들러 어댑터 실행**</p>
<ul>
<li>핸들러(@Controller 메서드)를 호출 가능한 형태로 변환하고 실행</li>
</ul>
<p><strong>4. 모델과 뷰 반환</strong></p>
<ul>
<li>컨트롤러의 실행 결과로 <code>ModelAndView</code> 객체 생성</li>
</ul>
<p><strong>5. 뷰 리졸버 처리</strong></p>
<ul>
<li><code>ViewResolver</code>를 통해 논리적 이름(view name)을 실제 뷰 파일로 변환</li>
</ul>
<p>** 6. 최종 렌더링**</p>
<ul>
<li>실제 뷰를 렌더링하고 클라이언트에게 응답 전송</li>
</ul>
<h2 id="💡dispatcherservlet-처리-흐름">💡DispatcherServlet 처리 흐름</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/eee05cb1-7220-4001-8175-e014ecae5d8e/image.png" /></p>
<ol>
<li>클라이언트의 요청을 <code>DispatcherServlet</code>이 받음</li>
<li><code>HandlerMapping</code>을 통해 해당 요청을 처리할 컨트롤러를 찾음</li>
<li>해당 컨트롤러가 실행할 수 있는 <code>HandlerAdapter</code>를 찾아서 전달</li>
<li><code>HandlerAdapter</code>가 컨트롤러로 요청을 위임</li>
<li>컨트롤러가 비즈니스 로직을 처리</li>
<li>컨트롤러가 반환값을 반환</li>
<li><code>HandlerAdapter</code>가 반환값을 처리</li>
<li>서버의 응답을 클라이언트로 반환</li>
</ol>
<h4 id="➡️1-클라이언트의-요청을-dispatcherservlet이-받음">➡️1. 클라이언트의 요청을 DispatcherServlet이 받음</h4>
<ul>
<li>디스패처서블릿은 가장 먼저 요청을 받는 프론트 컨트롤러로써, 클라이언트의 모든 요청을 가로챈다.<h4 id="➡️2-handlermapping을-통해-해당-요청을-처리할-컨트롤러를-찾음">➡️2. HandlerMapping을 통해 해당 요청을 처리할 컨트롤러를 찾음</h4>
<ul>
<li>DispacherServlet은 요청을 처리할 핸들러(컨트롤러)를 찾고 해당 객체의 메소드를 호출한다. 이 때, 어느 컨트롤러가 요청을 처리할 수 있는지 식별해야하는데, 그 역할을 하는것이 핸들러 매핑이다.</li>
<li>핸들러 매핑 구현체가 URL, HTTP 메서드 등을 기준으로 어떤 컨트롤러 메서드가 이 요청을 처리할지 결정하게 된다.</li>
</ul>
</li>
</ul>
<h4 id="➡️3-해당-컨트롤러가-실행할-수-있는-handleradapter를-찾아서-전달">➡️3. 해당 컨트롤러가 실행할 수 있는 HandlerAdapter를 찾아서 전달</h4>
<ul>
<li>다양한 핸들러(@Controller, @RestController, HttpRequsetHandler 등)을 일관된 방식으로 실행하기 위해 적절한 핸들러 어댑터를 찾는다.</li>
</ul>
<h4 id="➡️4-handleradapter가-컨트롤러로-요청을-위임">➡️4. HandlerAdapter가 컨트롤러로 요청을 위임</h4>
<ul>
<li>핸들러 어댑터가 컨트롤러로 요청을 위임한다.</li>
</ul>
<h4 id="➡️5-컨트롤러가-비즈니스-로직을-처리">➡️5. 컨트롤러가 비즈니스 로직을 처리</h4>
<ul>
<li>컨트롤러는 Service를 호출하고, 우리가 작성한 비즈니스 로직들을 처리한다.</li>
</ul>
<h4 id="➡️6-컨트롤러가-반환값을-반환">➡️6. 컨트롤러가 반환값을 반환</h4>
<ul>
<li>비즈니스 로직이 처리된 후에는 컨트롤러가 반환값을 반환한다.</li>
<li>응답데이터를 사용하는 경우에는 ResponseEntity를, 응답페이지를 보여주는 경우라면 view의 이름을 반환할 수도 있다.<h4 id="➡️7-handleradapter가-반환값을-처리">➡️7. HandlerAdapter가 반환값을 처리</h4>
</li>
<li>만약 컨트롤러가 ResponseEntity를 반환하면 MessageConverter를 사용해 객체를 직렬화하고 응답상태(HttpStatus)를 설정한다.</li>
<li>컨트롤러가 View 이름을 반환하면 ViewResolver를 통해 실제 뷰 파일 경로를 찾아 View 객체로 변환한다.</li>
</ul>
<h4 id="➡️8-서버의-응답을-클라이언트로-반환">➡️8. 서버의 응답을 클라이언트로 반환</h4>
<ul>
<li>HttpServletResponse를 통해 HTML, JSON, 파일 등 최종 응답 결과가 전송된다.</li>
</ul>