<h1 id="💡optional이란">💡Optional이란</h1>
<blockquote>
<p><code>Optional</code>은 Java8에서 도입된 null 처리의 대안 클래스로, 값이 있을 수도 있고 없을 수도 있음을 명시적으로 표현하기 위해 사용된다. null을 직접 다루는 것보다 <strong>안전하고 명확한 코드 작성</strong>을 도와준다.</p>
</blockquote>
<hr />
<h2 id="✅optional이-필요한-이유">✅Optional이 필요한 이유</h2>
<p>기존 자바에서는 null 체크하는 로직을 포함하는 코드가 자주 나왔다.</p>
<pre><code class="language-java">String name = getUserName();
if (name != null) {
    System.out.println(name.length());
}</code></pre>
<p>null 체크를 해야할 부분이 많다면 코드가 복잡해져 가독성이 떨어지고, 실수로 null 체크를 누락하면 <code>NullPointerException</code>이 발생한다.</p>
<blockquote>
<p>➡️<strong>이를 방지하기 위해 <code>Optional</code>이 등장했다.</strong></p>
</blockquote>
<hr />
<h2 id="✅optional-기본-구조">✅Optional 기본 구조</h2>
<pre><code class="language-java">public final class Optional&lt;T&gt; {
    private final T value;
    ...
}</code></pre>
<ul>
<li><code>Optional&lt;T&gt;</code>는 내부에 T타입의 객체를 하나 감싼다.</li>
<li>value가 <strong>null일 수도 있고, null이 아닐 수도 있는 객체</strong>를 감싸고 있는 Wrapper클래스다.</li>
</ul>
<hr />
<h2 id="✅optional의-주요-메서드">✅Optional의 주요 메서드</h2>
<table>
<thead>
<tr>
<th>메서드</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td>*<em><code>Optional.of(value)</code> *</em></td>
<td>주어진 값으로 Optional 객체를 생성 <br />단, <strong>value가 null이 아닌 경우</strong>에 사용</td>
</tr>
<tr>
<td><strong><code>Optional.ofNullable(value)</code></strong></td>
<td>주어진 값으로 Optional 객체를 생성 <br />단, <strong>value의 값이 null인 경우 Optional.empty()</strong>를 반환</td>
</tr>
<tr>
<td>*<em><code>Optional.empty()</code> *</em></td>
<td><strong>빈(empty) Optional 객체</strong>를 반환<br />값이 없음을 나타내는 경우에 사용</td>
</tr>
<tr>
<td><strong><code>isPresent()</code></strong></td>
<td><strong>Optional 객체에 값이 존재하는지 여부</strong> 확인<br />값이 존재하면 true, 값이 없으면 false 반환</td>
</tr>
<tr>
<td>*<em><code>isEmpty()</code>  *</em></td>
<td>Optional 객체에 값이 없는지 확인<br />!Optional.isPresent() 와 같음</td>
</tr>
<tr>
<td><strong><code>ifPresent(Consumer)</code></strong></td>
<td>값이 존재하면 동작 수행</td>
</tr>
<tr>
<td>*<em><code>orElse(default)</code> *</em></td>
<td>값이 존재하는 경우 해당 값을 반환하고, <br />값이 없는 경우 주어진 기본 값을 반환 <br />값이 존재 여부에 상관없이 항상 실행된다.</td>
</tr>
<tr>
<td>*<em><code>orElseGet(Supplier)</code>  *</em></td>
<td>값이 존재하는 경우 해당 값을 반환하고,<br />값이 없는 경우 주어진 Supplier로부터 기본 값을 가져옴. <br /><strong>값이 존재하지 않은 경우에만 실행</strong>된다.</td>
</tr>
<tr>
<td>*<em><code>orElseThrow()</code> *</em></td>
<td>값이 존재하는 경우 해당 값을 반환하고,<br /><strong>값이 없는 경우에는 예외 발생</strong> 시킴</td>
</tr>
<tr>
<td>*<em><code>map(Function)</code> *</em></td>
<td>값이 존재하는 경우에 주어진 Function에 따른<br />결과 값으로 <strong>새로운 Optional을 반환</strong></td>
</tr>
<tr>
<td><strong><code>filter(Predicate&lt;T&gt;)</code></strong></td>
<td>값이 존재하고, <strong>Predicate을 만족하는 경우에만 값을 반환</strong></td>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</tbody></table>
<hr />
<h3 id="1-optionalofvalue">1. Optional.of(value)</h3>
<blockquote>
<p>주어진 값으로 Optional객체를 생성한다. 주의할 점은 <strong>value값이 절대 null이 아닌 경우에만 사용되는 메서드</strong>이다. value값이 null인 경우 <code>NullPointerException</code>이 발생하게 된다.</p>
</blockquote>
<h4 id="📍사용예시">📍사용예시</h4>
<pre><code class="language-java">Optional&lt;String&gt; name = Optional.of(&quot;자바&quot;);
System.out.println(name); // 출력: Optional[자바]</code></pre>
<ul>
<li>vaulue가 null이면 NullPointerException 발생 </li>
</ul>
<pre><code class="language-java">Optional&lt;String&gt; name = Optional.of(null); // 예외 발생</code></pre>
<hr />
<h3 id="2-optionalofnullablevalue">2. Optional.ofNullable(value)</h3>
<blockquote>
<p>주어진 값으로 Optional 객체를 생성한다. Optional.of(value)와 달리 <strong>value값이 null 일 수도 있는 경우에 사용하는 메서드</strong>이다. value가 null인 경우 <code>Optional.empty()</code>를 반환한다.</p>
</blockquote>
<h4 id="📍사용예시-1">📍사용예시</h4>
<pre><code class="language-java">Optional&lt;String&gt; name = Optional.ofNullable(&quot;자바&quot;);
System.out.println(name); // 출력: Optional[자바]

Optional&lt;String&gt; none = Optional.ofNullable(null);
System.out.println(none); // null인 경우 출력: Optional.empty</code></pre>
<hr />
<h3 id="3-optionalempty">3. Optional.empty()</h3>
<blockquote>
<p> 값이 없음을 나타내는 <strong>빈(empty) Optional 객체를 반환</strong>한다. </p>
</blockquote>
<h4 id="📍사용예시-2">📍사용예시</h4>
<pre><code class="language-java">Optional&lt;String&gt; empty = Optional.empty();
System.out.println(empty); // 출력: Optional.empty</code></pre>
<hr />
<h3 id="4-ispresent-isempty">4. isPresent(), isEmpty()</h3>
<blockquote>
<p>Optional 객체에 <strong>값이 존재하는 지 여부를 확인할 때 사용하는 메서드</strong>
<code>isPresent()</code>는 <strong>값이 존재하면 true, 값이 없으면 flase</strong>를 반환
<code>isEmpty()</code>는 <strong>값이 존재하지 않으면 true, 값이 존재</strong>하면 false 반환</p>
</blockquote>
<h4 id="📍사용예시-3">📍사용예시</h4>
<pre><code class="language-java">Optional&lt;String&gt; name = Optional.of(&quot;자바&quot;);

if (name.isPresent()) {    //값이 존재하면 true
    System.out.println(&quot;값이 존재합니다.&quot;);
}

if(name.isEmpty()) { //값이 존재하지 않으면 true
    System.out.println(&quot;값이 존재하지 않는다.&quot;);
}
</code></pre>
<hr />
<h3 id="5-ifpresentconsumer">5. ifPresent(Consumer)</h3>
<blockquote>
<p>Optional 객체에 <strong>값이 존재하는 경우에만 주어진 Consumer를 수행</strong>한다. 즉, 값이 있으면 동작이 실행되고, 값이 없으면 동작을 수행하지 않는다.</p>
</blockquote>
<h4 id="📍사용예시-4">📍사용예시</h4>
<pre><code class="language-java">Optional&lt;String&gt; name = Optional.of(&quot;자바&quot;);
// 값이 존재하므로 ifPresent 동작 실행.
name.ifPresent(n -&gt; System.out.println(&quot;이름 -&gt; &quot; + n));  
// 출력 : 이름 -&gt; 자바
Optional&lt;String&gt; nullName = Optional.ofNullable(null);
//값이 null이므로 ifPresent 동작 실행하지 않음
nullName.ifPresent(n -&gt; System.out.println(&quot;이름 -&gt; &quot; + n));  </code></pre>
<hr />
<h3 id="6-orelsedefault">6. orElse(default)</h3>
<blockquote>
<p> Optional에 <strong>값이 존재하는 경우에는 해당 값을 반환하고, 값이 없는 경우에는 주어진 기본 값을 반환</strong>한다. 주의할 점은 Optional에 값이 존재하는 경우에도 <code>orElse()</code>가 실행되어 <strong>기본값을 무조건 생성</strong>한다. 따라서 비용이 큰 기본값을 얻을 때 사용하기 보다 단순한 기본 값을 얻고자 할 때 사용하는 것이 일반적이다.</p>
</blockquote>
<h4 id="📍사용예시-5">📍사용예시</h4>
<pre><code class="language-java">Optional&lt;String&gt; name = Optional.of(&quot;자바&quot;);
String result = name.orElse(&quot;기본값&quot;);
System.out.println(result); // 출력: 자바
</code></pre>
<hr />
<h3 id="7-orelsegetsupplier">7. orElseGet(Supplier)</h3>
<blockquote>
<p>Optional에 값이 <strong>존재하는 경우에는 해당 값을 반환하고, 값이 없는 경우에는 주어진 Supplier로부터 기본 값을 가져온다.</strong> <code>orElse()</code>와는 달리 <strong>Optional에 값이 없는 경우에만 Supplier가 호출</strong>되어 기본값을 생성한다.</p>
</blockquote>
<h4 id="📍사용예시-6">📍사용예시</h4>
<pre><code class="language-java">String value = Optional.ofNullable(null).orElseGet(() -&gt; &quot;기본값&quot;);
System.out.println(value); // 출력: 기본값</code></pre>
<hr />
<h3 id="8-orelsethrow">8. orElseThrow()</h3>
<blockquote>
<p> Optional에 값이 존재하는 경우에는 해당 값을 반환하고, <strong>값이 없는 경우에는 예외를 발생</strong></p>
</blockquote>
<h4 id="📍사용예시-7">📍사용예시</h4>
<pre><code class="language-java">String value = Optional.ofNullable(null)
    .orElseThrow(() -&gt; new IllegalArgumentException(&quot;값이 없습니다&quot;));</code></pre>
<ul>
<li>Optional 객체가 null이므로 <code>IllegalArgumentException</code> 예외 발생</li>
</ul>
<hr />
<h3 id="9-mapfunctiont-r">9. map(Function&lt;T, R&gt;)</h3>
<blockquote>
<p> Optional 객체에 값이 존재하는 경우에 주어진 Function에 따른 결과 값으로 <strong>새로운 Optional을 반환</strong></p>
</blockquote>
<h4 id="📍사용예시-8">📍사용예시</h4>
<pre><code class="language-java">Optional&lt;String&gt; name = Optional.of(&quot;자바&quot;);

Optional&lt;Integer&gt; nameLength = name.map(String::length);
System.out.println(nameLength); // 출력: Optional[2]</code></pre>
<ul>
<li>name안에 있는 값이 존재할 경우만 그 값을 꺼내서 <code>String::length</code>함수를 적용( &quot;자바&quot; -&gt; 2)</li>
<li>그 2를 다시 Optional로 감싸서 <code>nameLength</code>가 됨.</li>
<li><strong>값을 변환하여 새로운 Optinal로 반환</strong></li>
</ul>
<hr />
<h3 id="10-filterpredicatet">10. filter(Predicate)</h3>
<blockquote>
<p> Optional 객체에 <strong>값이 존재하고, 주어진 Predicate을 만족하는 경우에 기존 Optional을 반환</strong>합니다. 그렇지 않은 경우에는 Optional.empty()를 반환합니다.</p>
</blockquote>
<h4 id="📍사용예시-9">📍사용예시</h4>
<pre><code class="language-java">//조건을 만족하지 못하는 코드
Optional&lt;String&gt; name = Optional.of(&quot;자바&quot;);
Optional&lt;String&gt; filtered = name.filter(n -&gt; n.length() &gt; 2);
System.out.println(filtered); // 출력: Optional.empty</code></pre>
<p>위 코드에서는 Optional 안에 들어있는 값(자바)가 **조건을 만족하는지 검사하고 조건을 만족하면 값을 그대로 유지하고, 만족하지 않으면 Optional.empty()를 리턴</p>
<ul>
<li>name은 &quot;자바&quot;를 감싸고 있음. </li>
<li>filter의 조건(<code>n.length() &gt; 2</code>) -&gt; 문자열의 길이가 2보다 크다.를 충족해야함</li>
<li>&quot;자바&quot;의 길이는 2 -&gt; <strong>조건 만족하지 않음</strong></li>
<li>출력 결과는 <code>Optional.empty</code></li>
</ul>
<pre><code class="language-java">//조건을 만족하는 코드
Optional&lt;String&gt; name = Optional.of(&quot;스프링&quot;);
Optional&lt;String&gt; filtered = name.filter(n -&gt; n.length() &gt; 2);
System.out.println(filtered); // 출력: Optional.empty</code></pre>
<ul>
<li>name이 필터의 조건 (<code>n.length() &gt; 2</code>)을 충족하므로 출력 결과는 <code>Optional[스프링]</code> </li>
</ul>
<hr />
<h2 id="✅-optional-사용-시-주의사항">✅ Optional 사용 시 주의사항</h2>
<h3 id="1-리턴값으로만-쓰기를-권장">1. 리턴값으로만 쓰기를 권장</h3>
<p><strong>➡️메소드의 매개변수 타입, 맵의 키 타입, 인스턴스 필드 타입으로 사용하지 않는 것이 좋다.</strong></p>
<pre><code class="language-java">// ❌권장되지 않음 : 매개변수 타입으로 사용
public void process(Optional&lt;String&gt; input) {
    ...  
}

// ⭕권장됨
public void process {
    ...  
}  </code></pre>
<hr />
<h3 id="2-optional을-리턴하는-메소드에서-null을-리턴하지-말-것">2. Optional을 리턴하는 메소드에서 null을 리턴하지 말 것</h3>
<p><strong>➡️Optional을 반환하는 메소드에서 절대로 null을 반환하지 않도록 한다.</strong></p>
<pre><code class="language-java">// ❌권장되지 않음 : null 리턴
public Optional&lt;String&gt; getVlaue() {
    return null; 
}

// ⭕권장됨  
public Optional&lt;String&gt; getVlaue() {
    return Optional.ofNullable(null);     //null일 경우 빈 Optional을 반환
}  </code></pre>
<hr />
<h3 id="3--optionalget-직접-사용-지양할-것">3.  Optional.get() 직접 사용 지양할 것</h3>
<p><strong>➡️<code>Optional.get()</code>을 직접 사용하면 값이 없을 때 예외가 발생하므로 직접 사용을 지양한다.</strong></p>
<pre><code class="language-java">// ❌권장되지 않음
Optional&lt;String&gt; name = Optional.empty();
String value = name.get();  // NoSuchElementException 발생

// ⭕권장됨  
Optional&lt;String&gt; name = Optional.of(&quot;자바&quot;);
String value = name.orElse(&quot;기본값&quot;);  // 값이 있으면 그대로, 없으면 기본값
System.out.println(value); // 출력: 자바</code></pre>
<hr />
<h3 id="4-collection-map-stream-array-optional을-optional로-감싸지-말-것">4. Collection, Map, Stream, Array, Optional을 Optional로 감싸지 말 것</h3>
<p>*<em>➡️Collection, Map, Stream, Array, Optional을 다시 Optional로 감싸지 않도록 한다. *</em></p>
<pre><code class="language-java">// ❌권장되지 않음
public Optional&lt;Optional&lt;String&gt;&gt; getOptionalValue() {
    Optional&lt;String&gt; value = Option.of(&quot;test&quot;);
      return Optional.of(value);     //중첩 사용 ❌
}

// ⭕권장됨  
public Optiona&lt;String&gt; getOptionalValue() {
      return Optional.of(value); 
}</code></pre>