<blockquote>
<p>자바에서는 대표적으로 문자열을 다루는 자료형 클래스로 <code>String</code>, <code>StringBuffer</code>, <code>StringBuilder</code>가 있다. 세가지 클래스 모두 문자열을 다루는데 사용되지만 사용 목적에 따라  쓰임새가 다르다. 
그렇다면 왜 이렇게 굳이 여러 클래스를 만들어 놓은건지, 그 차이점과 특징에 대해 알아보도록 하자.</p>
</blockquote>
<h1 id="💡stringbuffer와-stringbuilder">💡StringBuffer와 StringBuilder</h1>
<p>StringBuffer / StringBuilder 클래스는 문자열을 연산(추가하거나 변경)할 때 주로 사용하는 자료형이다.
물론 <code>String</code> 자료형으로도 <code>+</code>, <code>concat()</code>을 이용하여 문자열을 이어붙일 수 있지만, 아래에서 설명할 불변 자료형인<code>String</code>의 특성상 <strong>문자열을 이어붙일 때마다 매번 내용이 합쳐진 새로운 String 인스턴스를 생성하게 된다</strong>. 따라서 <code>String</code>은 문자열의 결합이 많아질수록 <strong>공간이 낭비되고 속도 또한 느려지게 된다</strong>.</p>
<pre><code class="language-java">String result = &quot;&quot;;
result += &quot;hello&quot;;
result += &quot; &quot;;
result += &quot;jump to java&quot;;
System.out.println(result); // hello jump to java
// → 문자열이 합쳐질때마다 새로운 인스턴스 생성</code></pre>
<p>그래서 자바에서는 문자열 연산을 전용으로 하는 <code>StringBuffer</code>/<code>StringBuilder</code> 클래스가 존재한다. <code>StringBuffer</code> 클래스는 내부적으로 <strong>buffer(데이터를 임시로 저장하는 메모리)라고 하는 독립적인 공간을 가지게 되어 문자열을 바로 추가할 수 있어 공간의 낭비도 없으며 문자열 연산 속도도 매우 빠르다는 특징</strong>이 있다.</p>
<pre><code class="language-java">StringBuffer sb = new StringBuffer();  // StringBuffer 객체 sb 생성
sb.append(&quot;hello&quot;);
sb.append(&quot; &quot;);
sb.append(&quot;jump to java&quot;);
String result = sb.toString();
System.out.println(result); // hello jump to java
// → + 연산보다는 복잡해 보이지만 연산 속도가 빠름</code></pre>
<blockquote>
<p><code>StringBuffer</code>와 비슷한 자료형으로 <code>StringBuilder</code>가 있는데, StringBuilder 사용법은 StringBuffer와 동일하다. StringBuffer와 StringBuilder의 차이는 뒤에서 다뤄보겠다.</p>
</blockquote>
<hr />
<h2 id="✅stringbuffer-내장-메서드">✅StringBuffer 내장 메서드</h2>
<pre><code class="language-java">String str = &quot;abcdefg&quot;;
StringBuffer sb = new StringBuffer(str); // String -&gt; StringBuffer

System.out.println(&quot;처음 상태 : &quot; + sb); // 처음상태 : abcdefg

System.out.println(&quot;문자열 String 변환 : &quot; + sb.toString()); // StringBuffer를 String으로 변환하기

System.out.println(&quot;문자열 추출 : &quot; + sb.substring(2,4)); // 문자열 추출하기

System.out.println(&quot;문자열 추가 : &quot; + sb.insert(2,&quot;추가&quot;)); // 문자열 추가하기

System.out.println(&quot;문자열 삭제 : &quot; + sb.delete(2,4)); // 문자열 삭제하기

System.out.println(&quot;문자열 연결 : &quot; + sb.append(&quot;hijk&quot;)); // 문자열 붙이기

System.out.println(&quot;문자열의 길이 : &quot; + sb.length()); // 문자열의 길이구하기

System.out.println(&quot;용량의 크기 : &quot; + sb.capacity()); // 용량의 크기 구하기

System.out.println(&quot;문자열 역순 변경 : &quot; + sb.reverse()); // 문자열 뒤집기

System.out.println(&quot;마지막 상태 : &quot; + sb); // 마지막상태 : kjihgfedcba</code></pre>
<ul>
<li>뒤에서 후술할 <code>StringBuilder</code> 클래스의 메서드 사용법과 동일하다.</li>
</ul>
<hr />
<h1 id="💡string-vs-stringbuffer--stringbuilder">💡String vs StringBuffer / StringBuilder</h1>
<blockquote>
<p>String과 StringBuffer / StringBuilder의 큰 차이점은 <strong>가변자료형이냐, 불변자료형이냐</strong>이다.</p>
</blockquote>
<h2 id="✅stirng--불변-자료형">✅Stirng : 불변 자료형</h2>
<p>기본적으로 <strong>자바에서는 String 객체의 값을 변경할 수 없다.</strong>
이는 한번 할당된 공간이 변하지 않는다고해서 <strong>불변(immutable) 자료형</strong>이라고 한다.
아래 예제를 보면, 단순히 변수 str이 참조하는 값에 <code>World</code> 문자열을 더해서 str 변수를 업데이트 시킨것으로 볼 수 있는데, 실제 메모리에서는 <code>hello world</code>값을 저장할 영역을 따로 만들고 변수 str을 다시 참조하는 식으로 동작한다.</p>
<pre><code class="language-java">String str = &quot;hello&quot;;
str = str + &quot; world&quot;;

System.out.println(str); // hello world</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/464d3a6c-26a1-43a1-a614-ad410a3e1f51/image.png" /></p>
<p>이러한 이유로 <strong>String 객체는 문자열 연산이 많은 경우 성능이 좋지 않다.</strong>
하지만 불변 객체는 간단하게 사용가능하고, 동기화에 대해 신경쓰지 않아도 되기때문에(Thread-safe), 내부 데이터를 자유롭게 공유 가능하다.</p>
<h2 id="✅stringbuffer--stringbuilder--가변-자료형">✅StringBuffer / StringBuilder : 가변 자료형</h2>
<p>StringBuffer나 StringBuilder의 경우 문자열 데이터를 다룬다는 점에서 String 객체와 같지만, 객체의 공간이 부족해지는 경우 버퍼의 크기를 유연하게 늘려주어 <strong>가변(mutable)적</strong>이라는 차이점이 있다.
두 클래스는 내부 buffer에 문자열을 저장해두고 그 안에서 추가,수정,삭제 작업을 할 수 있도록 설계되어 있다.
String 객체는 한번 생성되면 불변적인 특징 때문에 값을 업데이트할때마다 새로운 문자열을 가진 인스턴스가 생성되어 메모리 공간을 차지하지만 <strong>StringBuffer / StringBuilder는 가변성을 가지기 때문에 동일 객체 내에서 문자열 크기를 변경하는 것이 가능</strong>하다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/edb36f85-b095-4689-a8c3-506a0d79214d/image.png" /></p>
<p>따라서, <strong>문자열의 추가, 수정, 삭제가 빈번하게 발생할 경우라면 String이 아닌 StringBuffer / StringBuilder을 사용하는 것이 좋다.</strong></p>
<h2 id="✅그렇다면-stringbuffer--stringbuilder의-차이점은">✅그렇다면 StringBuffer / StringBuilder의 차이점은?</h2>
<blockquote>
<p>StringBuffer / StringBuilder 둘다 가변 자료형이며, 내부 buffer를 사용해서 문자열 작업을 하고, 사용법도 거의 비슷하다. 그렇다면 둘의 차이는 뭘까?</p>
</blockquote>
<p><strong>바로 멀티스레드에서 안전하냐, 안전하지않느냐의 차이 딱 한가지다.</strong>
결과를 바로 말하자면, </p>
<ul>
<li><strong>StringBuffer는 스레드에서 안전하다.</strong></li>
<li><strong>StringBuilder는 스레드에서 안전하지 않다.</strong></li>
</ul>
<p><strong>StringBuffer는 각 메서드 별로 동기화를 지원</strong>하여 멀티스레드 환경에서도 안전하게 동작하며, <strong>StringBuilder는 동기화를 지원하지 않는다.</strong>
그렇기 때문에 멀티스레드 환경이라면 값 동기화 보장을 위해 StringBuffer를 사용하고, 단일 스레드 환경이라면 StringBuilder를 사용하는 것을 권장한다.</p>
<p>*<em>단순히 성능만 놓고 본다면 연산이 많은 경우 StringBuilder  &gt; StringBuffer &gt;&gt;&gt; String *</em></p>
<hr />
<h1 id="☑️string-stringbuffer-stringbuilder-정리">☑️String, StringBuffer, StringBuilder 정리</h1>
<table border="1" cellpadding="5" cellspacing="0">
  <thead>
    <tr>
      <th>차이점</th>
      <th>String</th>
      <th>StringBuffer</th>
      <th>StringBuilder</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><b>선언 방식</b></td>
      <td><code>String str = "Hello";</code></td>
      <td><code>StringBuffer sb = new StringBuffer("Hello");</code></td>
      <td><code>StringBuilder sb = new StringBuilder("Hello");</code></td>
    </tr>
    <tr>
      <td><b>문자열 변경 방법</td>
      <td><code>str += " World";</code></td>
      <td><code>sb.append(" World");</code></td>
      <td><code>sb.append(" World");</code></td>
    </tr>
    <tr>
      <td><b>클래스 종류</td>
      <td>불변(immutable)한 클래스</td>
      <td>가변(mutable)한 클래스</td>
      <td>가변(mutable)한 클래스</td>
    </tr>
    <tr>
      <td><b>변수의 추가 및 변경 시 메모리 성능</td>
      <td>불리함</td>
      <td>유리함</td>
      <td>유리함</td>
    </tr>
    <tr>
      <td><b>단일 쓰레드 환경에서 성능</td>
      <td>안정적</td>
      <td>안정적</td>
      <td>안정적</td>
    </tr>
    <tr>
      <td><b>멀티 쓰레드 환경에서 성능</td>
      <td>안정적</td>
      <td>안정적, 동기화 과정에서 성능 저하</td>
      <td>안정적, 동기화 과정에서 성능 저하 X</td>
    </tr>
    <tr>
      <td><b>속도</td>
      <td>매우 느림</td>
      <td>빠름</td>
      <td>빠름</td>
    </tr>
    <tr>
      <td><b>사용 목적</td>
      <td>문자열 추가와 변경이 발생하지 않는 경우 사용하는 것이 좋다.</td>
      <td>멀티쓰레드 환경에서 문자열의 변수의 추가와 변경이 자주 발생하는 경우 사용하면 좋다.</td>
      <td>단일쓰레드 환경에서 문자열의 변수의 추가와 변경이 자주 발생하는 경우 사용하면 좋다.</td>
    </tr>
  </tbody>
</table>

<hr />
<h4 id="☑️string-stringbuilder-stringbuffer의-차이점은-무엇인가요">☑️String, StringBuilder, StringBuffer의 차이점은 무엇인가요?</h4>
<p>Java에서 문자열을 다룰 때 사용하는 String, StringBuilder, StringBuffer는 각각 특징이 다릅니다.
String은 불변 객체라 한번 생성되면 변경할 수 없습니다. 따라서 문자열을 더하거나 수정할 때마다 새로운 객체가 생성되어 메모리와 성능상 비용이 발생합니다. 변경이 거의 없는 상수나 리터럴을 다룰 때 적합합니다.
StringBuilder, StringBuffer는 가변 객체라 내부 buffer에서 문자열 작업을 효율적으로 진행할 수 있습니다. 두 클래스의 차이점은 동기화 여부입니다.
StringBuilder는 동기화를 지원하지 않아 멀티스레드 환경에서 안전하지 않지만, 단일 스레드 환경에서는 가장 빠릅니다.
StringBuffer는 각 메서드별로 동기화를 지원하여 멀티스레드 환경에서 안전하게 사용할 수 있습니다.</p>