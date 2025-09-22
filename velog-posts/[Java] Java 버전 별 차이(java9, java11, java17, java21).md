<h1 id="💡java-lts-버전">💡Java LTS 버전</h1>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/36c18f29-cf93-4fc4-afbd-4c899752aa41/image.png" /></p>
<p>자바는 3년정도의 텀으로 <strong>LTS(Long Term Support) 릴리스</strong>가 제공된다.
LTS란 <code>Long Term Support</code>의 약자로 장기 지원 버전을 의미한다. 자바의 LTS 버전은 특정 기간 동안 안정적인 지원과 업데이트를 제공받을 수 있다. 일반적으로 3년마다 출시되며, 출시 후 5년동안 기술 지원이 제공된다.</p>
<hr />
<h2 id="✅lts버전을-사용해야-하는-이유는">✅LTS버전을 사용해야 하는 이유는?</h2>
<ol>
<li><p>장기적인 지원
Long Term Support란 이름과 같이 장기적으로 보안 업데이트와 버그 수정을 지원해준다</p>
</li>
<li><p>안정성
광범위하게 테스트되고 안정화된 후 릴리스되므로 높은 신뢰성을 갖고 있다.</p>
</li>
<li><p>기업 환경에 적합
많은 기업에서 규제 요구 사항을 충족하기 위해 장기적으로 지원되는 소프트웨어를 사용해야 한다</p>
</li>
<li><p>커뮤니티 및 생태계 지원
LTS 버전은 많은 개발자와 기업에서 사용되므로 강력한 커뮤니티와 생태계를 갖고있다. 
또한, 주요 라이브러리와 프레임 워크가 LTS 버전을 우선적으로 지원하기 때문에 호환성이 높다.</p>
</li>
</ol>
<hr />
<h1 id="💡자바-버전별-특징">💡자바 버전별 특징</h1>
<h2 id="📌java-8">📌Java 8</h2>
<blockquote>
<p>Java 8의 변경점으로는 크게 람다 표현식과 함수형 프로그래밍 지원이 있다. 이로 인해 코드의 가독성이 높아지고 간결한 코드를 작성할 수 있게 되었다.</p>
</blockquote>
<h3 id="1️⃣람다-표현식lambda-expressions">1️⃣람다 표현식(Lambda Expressions)</h3>
<ul>
<li>함수형 프로그래밍을 지원하여 코드의 간결성과 가독성을 높임<pre><code class="language-java">Arrays.sort(strArray, (String s1, String s2) -&gt; s2.length() - s1.length());</code></pre>
</li>
</ul>
<h3 id="2️⃣디폴트-메서드default-methods">2️⃣디폴트 메서드(Default Methods)</h3>
<ul>
<li>인터페이스에 기본 메서드를 정의하여 기존 코드를 깨지 않고 기능을 추가할 수 있다.<pre><code class="language-java">default public Stream&lt;T&gt; stream() {
return StreamSupport.stream(spliterator(), false);
}</code></pre>
</li>
</ul>
<h3 id="3️⃣스트림-api-stream-api">3️⃣스트림 API (Stream API)</h3>
<ul>
<li>컬렉션 데이터의 반복, 필터링, 매핑 등을 간단하게 처리할 수 있는 API 도입<pre><code class="language-java">List&lt;String&gt; filtered = list.stream()
.filter(s -&gt; s.startsWith(&quot;a&quot;))
.collect(Collectors.toList());</code></pre>
</li>
</ul>
<h3 id="4️⃣optional-클래스">4️⃣Optional 클래스</h3>
<ul>
<li>null 참조를 다루기 위한 컨테이너 클래스로, 보다 안전한 코드를 작성할 수 있다.</li>
<li><a href="https://velog.io/@dev_ssj/Java-Optional%EC%9D%B4%EB%9E%80">참고</a><pre><code class="language-java">Optional&lt;String&gt; optional = Optional.of(&quot;Hello&quot;);
optional.ifPresent(System.out::println);</code></pre>
</li>
</ul>
<hr />
<h2 id="📌java-11">📌Java 11</h2>
<h3 id="1️⃣지역-변수-유형-추론var-키워드">1️⃣지역 변수 유형 추론(var 키워드)</h3>
<ul>
<li>변수 선언 시 타입을 명시하지 않아도 컴파일러가 자동으로 타입을 추론해주는 기능 도입<pre><code class="language-java">var list = new ArrayList&lt;String&gt;();
list.add(&quot;Hello, Java 11&quot;);</code></pre>
</li>
</ul>
<h3 id="2️⃣새로운-http-클라이언트-api">2️⃣새로운 HTTP 클라이언트 API</h3>
<ul>
<li><code>java.net.http</code> 패키지에 새로운 HTTP 클라이언트를 도입하여 HTTP/2 및 WebSocket을 지원<pre><code class="language-java">HttpClient client = HttpClient.newHttpClient();
HttpRequest request = HttpRequest.newBuilder()
.uri(URI.create(&quot;https://example.com&quot;))
.build();
HttpResponse&lt;String&gt; response = client.send(request, BodyHandlers.ofString());
System.out.println(response.body())</code></pre>
</li>
</ul>
<hr />
<h2 id="📌java-17">📌Java 17</h2>
<h3 id="1️⃣패턴-매칭-for-switch">1️⃣패턴 매칭 for switch</h3>
<ul>
<li>switch 문에서 패턴 매칭을 사용하여 코드의 가독성을 높임<pre><code class="language-java">switch (obj) {
case Integer i -&gt; System.out.println(&quot;Integer: &quot; + i);
case String s -&gt; System.out.println(&quot;String: &quot; + s);
default -&gt; System.out.println(&quot;Unknown type&quot;);
}</code></pre>
</li>
</ul>
<h3 id="2️⃣sealed-classes">2️⃣Sealed Classes</h3>
<ul>
<li>상속 구조를 제한하여 더 안전하고 예측 가능한 클래스 계층을 설계할 수 있게 함<pre><code class="language-java">public abstract sealed class Shape permits Circle, Square {}</code></pre>
</li>
</ul>
<h3 id="3️⃣record-patterns">3️⃣Record Patterns</h3>
<ul>
<li>간단하게 불변 데이터 클래스를 정의할 수 있는 기능 정식 도입<pre><code class="language-java">public record Point(int x, int y) {}</code></pre>
</li>
</ul>
<hr />
<h2 id="📌java-21">📌Java 21</h2>
<h3 id="1️⃣virtual-threads">1️⃣Virtual Threads</h3>
<ul>
<li>고성능 동시성을 제공하는 새로운 경량 스레드 모델 도입<pre><code class="language-java">Thread.startVirtualThread(() -&gt; {
System.out.println(&quot;Hello from a virtual thread&quot;);
});</code></pre>
</li>
</ul>
<h3 id="2️⃣record-patterns">2️⃣Record Patterns</h3>
<ul>
<li>레코드 패턴을 활용한 더 나은 데이터 추출 및 처리 가능</li>
</ul>
<h3 id="3️⃣foreign-function--memory-api">3️⃣Foreign Function &amp; Memory API</h3>
<ul>
<li>자바 애플리케이션이 외부 메모리와 상호 작용하는 새로운 방식 제공</li>
</ul>