<h2 id="💡프론트-컨트롤러란">💡프론트 컨트롤러란?</h2>
<blockquote>
<p>웹 애플리케이션에서 클라이언트의 <strong>모든 요청을 하나의 진입 지점에서 받아 처리</strong>하는 디자인 패턴이다. 즉, <strong>공통된 관심사</strong>를 모아주는 역할을 해서 요청 흐름의 중앙 집중화를 통해 구조를 단순화하고 공통 기능을 쉽게 관리할 수 있도록 도와준다.</p>
</blockquote>
<h2 id="✅프론트-컨트롤러-도입-배경">✅프론트 컨트롤러 도입 배경</h2>
<h3 id="📍-컨트롤러-도입-전">📍 <strong>컨트롤러 도입 전</strong></h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/0918c422-4e7f-40bc-922c-b9502d17e64f/image.png" /></p>
<ul>
<li>프론트 컨트롤러 도입 이전엔 각 컨트롤러마다 공통으로 처리가능한 로직을 하나씩 설계 해주었어야 했다.</li>
<li>클라이언트가 어떤 컨트롤러로 들어올지 모르기때문에 항상 위와 같은 방식을 따라야했고, 그 결과** 공통된 로직을 중복 작성**한다는 문제점이 있었다.</li>
</ul>
<h3 id="📍-컨트롤러-도입-이후">📍 <strong>컨트롤러 도입 이후</strong></h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/bcc3f0ea-2ea9-46b4-82c8-fefb4b9b1902/image.png" /></p>
<ul>
<li>프론트 컨트롤러에 공통 처리 기능 로직을 모두 몰아주고, 이후에 클라이언트의 각 요청에 맞는 컨트롤러로 보내주어 처리하는 <strong>프론트 컨트롤러 패턴</strong>이 도입되었다.</li>
<li>스프링 MVC에서는 <code>DispatcherServlet</code>이 <strong>프론트 컨트롤러 역할</strong>을 수행한다.
<a href="https://velog.io/@dev_ssj/DispatcherServlet-%EC%9D%B4%EB%9E%80">DispatcherServlet이란?</a></li>
</ul>
<h2 id="✅프론트-컨트롤러의-장점">✅프론트 컨트롤러의 장점</h2>
<ol>
<li><strong>공통 처리</strong>를 모든 컨트롤러에 반복하지 않고 한 곳에서 처리 가능</li>
<li><strong>유지보수</strong>가 쉬움</li>
<li><strong>확장성</strong>과 <strong>일관성</strong> 확보</li>
</ol>