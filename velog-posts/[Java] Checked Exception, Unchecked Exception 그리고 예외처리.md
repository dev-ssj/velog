<h1 id="💡프로그램-오류">💡프로그램 오류</h1>
<p>프로그램은 어떤 원인에 의해 오작동 또는 비정상적 종료가 일어나는 경우가 있다.
이러한 결과를 초래하는 원인을 오류 또는 에러라고하며 발생 시점에 따라 3가지로 나뉜다.</p>
<ul>
<li><strong>컴파일 에러</strong> : 컴파일 시에 발생하는 오류</li>
<li><strong>런타임 에러</strong> : 실행 시 발생하는 오류</li>
<li><strong>논리적 에러</strong> : 실행은 되지만 의도와 다르게 동작하는것</li>
</ul>
<p>컴파일러는 소스코드(.java)의 오타, 잘못된 구문, 자료형 체크 등 기본적인 검사를 수행하여 오류가 있는지 알려주는데, 이 때 발생하는 것이 <strong>컴파일 에러</strong>다.
그리고 이 컴파일 과정이 끝나면 클래스파일(.class)이 생성되고, 생성된 클래스 파일을 실행할 수 있게 된다. 하지만 컴파일에는 문제가 없더라도, 프로그램 실행 중에 에러가 발생해서 잘못된 결과를 얻거나, 혹은 외부적인 요인으로 프로그램이 비정상적으로 종료될 수 있다. 이 때 발생하는 것이 <strong>실행 오류(런타임 에러)</strong>이다.</p>
<hr />
<h1 id="💡에러error와-예외exception">💡에러(Error)와 예외(Exception)</h1>
<ul>
<li>에러(Error) : 프로그램 코드에 의해서 수습될 수 없는 심각한 오류 </li>
<li>예외(Exception) : 프로그램 코드에 의해서 수습될 수 있는 다소 미약한 오류
에러는 메모리 부족이나, 스택 오버플로우와 같이 일단 <strong>발생하면 복구할 수 없는 심각한 오류</strong>이고, 개발자가 예측할 수 없다. <strong>예외는 발생하더라도 적절한 코드(try-catch)를 미리 작성해놓음으로써 프로그램의 비정상적 종료를 막을 수 있는 비교적 덜 심각한 오류</strong>이다.</li>
</ul>
<hr />
<h2 id="✅에러와-예외-클래스-계층-구조">✅에러와 예외 클래스 계층 구조</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/c4343be9-0a89-4fa5-b8b1-c1be87905c1c/image.png" /></p>
<p>자바에서는 실행 시 발생할 수 있는 오류(Runtime Error)를 Exception과 Error 클래스로 정의해놓았다.
Exception 클래스는 <code>Checked Exception</code>와 <code>Unchecked Exception</code>로 나눌 수 있다.</p>
<ul>
<li>Exception 클래스와 자손들(RuntimeException 클래스와 그 자손들 제외) ➡️ <strong>Checked Exception</strong></li>
<li>RuntimeException 클래스와 자손들 ➡️ <strong>Unchecked Exception</strong></li>
</ul>
<hr />
<h2 id="✅checked-exception와-unchecked-exception">✅Checked Exception와 Unchecked Exception</h2>
<h3 id="📌checked-exception">📌Checked Exception</h3>
<ul>
<li><strong>컴파일러가 예외처리를 강제하는 예외</strong></li>
<li>사용자(외부)의 동작에 의해서 발생될 수 있는 예외</li>
<li>ex)존재하지 않는 파일의 입력(<code>FileNotFoundException</code>), 클래스 이름을 잘못 입력(<code>ClassNotFoundException</code>) ...</li>
</ul>
<h3 id="📌unchecked-exception">📌Unchecked Exception</h3>
<ul>
<li><strong>컴파일러가 예외처리를 강제하지 않는 예외</strong></li>
<li>프로그래머의 실수에 의해 발생될 수 있는 예외</li>
<li>ex)null인 참조변수의 멤버호출(<code>NullPointException</code>), 배열의 범위를 벗어난 동작(<code>ArrayIndexOutOfBoundException</code>) 등</li>
</ul>
<table>
<thead>
<tr>
<th>구분</th>
<th>Checked Exception</th>
<th>Unchecked Exception</th>
</tr>
</thead>
<tbody><tr>
<td>확인 시점</td>
<td>컴파일 시점</td>
<td>런타임 시점</td>
</tr>
<tr>
<td>처리 여부</td>
<td>반드시 예외처리를 해주어야 함 (<code>try-catch</code> 또는 <code>throws</code>)</td>
<td>명시적으로 하지 않아도 됨</td>
</tr>
<tr>
<td>트랜잭션 처리 (Spring 기본)</td>
<td>예외 발생 시 <strong>Rollback 하지 않음</strong></td>
<td>예외 발생 시 <strong>Rollback 함</strong></td>
</tr>
<tr>
<td>대표 종류</td>
<td><code>IOException</code>, <code>SQLException</code>, <code>ParseException</code> 등</td>
<td><code>NullPointerException</code>, <code>IndexOutOfBoundsException</code>, <code>IllegalArgumentException</code> 등</td>
</tr>
</tbody></table>
<hr />
<h1 id="💡예외-처리-전략">💡예외 처리 전략</h1>
<blockquote>
<p>예외 처리란, <strong>프로그램 실행 시 예외 발생에 대비한 코드를 작성</strong>하는 것이다.</p>
</blockquote>
<h2 id="1️⃣예외-복구--try-catch">1️⃣예외 복구 : try-catch</h2>
<pre><code class="language-java">try {
    // 예외가 발생할 가능성이 있는 코드
} catch(Exception1 e1) {
    // 예외1이 발생했을 경우, 처리하기 위한 코드
} catch(Exception2 e1) {
    // 예외2이 발생했을 경우, 처리하기 위한 코드
} catch(Exception3 e1) {
    // 예외3이 발생했을 경우, 처리하기 위한 코드
} finally {
    // 예외 발생여부에 관계없이 항상 수행되어야하는 문장을 넣는다. (선택 사항)
}</code></pre>
<ul>
<li>예외 복구란 <code>try-catch-finally</code> 문을 사용하여 <strong>예외상황을 파악하고 정상상태로 되돌려</strong> 놓는 방법을 뜻한다.</li>
<li>여러 종류의 예외를 처리할 수 있도록 여러 catch 블럭을 작성할 수 있으며, 예외의 종류와 일치하는 단 한개의 catch 블럭만 수행된다.</li>
<li>만일 일치하는 예외가 없다면 프로그램은 비정상적으로 종료된다.</li>
<li>finally 블럭은 try던 catch던 항상 실행된다.</li>
</ul>
<h2 id="2️⃣예외처리-회피--throws">2️⃣예외처리 회피 : throws</h2>
<pre><code class="language-java">void method() throws Exception1, Exception2, Exception3 {
    ...
}</code></pre>
<ul>
<li>예외처리 회피란 <strong>예외처리를 직접 담당하지 않고 호출한 쪽으로 던져서 회피하는 방법</strong>을 뜻한다.</li>
<li>예외를 처리하는게 아닌, <strong>예외 발생 시 자신을 호출한 클라이언트에게 예외를 전달하여 처리를 떠맡기는 것</strong>이다.</li>
<li>만일 전달받은 클라이언트 코드마저 예외를 처리하지 않는다면 프로그램은 비정상적으로 종료된다.</li>
<li>해당 예외 뿐 아니라 자손 타입의 예외까지 발생할 수 있다는 점을 주의해야한다.</li>
</ul>
<h2 id="3️⃣예외-전환--throw-new">3️⃣예외 전환 : throw new</h2>
<pre><code class="language-java">//1.여러가지 예외를 하나의 큰 분류의 예외로 묶어서 다룸
catch(SpaceException e){
    InstallException ie = new InstallException(&quot;설치 중 예외발생&quot;);
    ie.initCause(e);
    throw ie;
}

//2.checked exception을 unchecked exception으로 바꿈
throw new RuntimeException(new MemoryException(&quot;메모리가 부족합니다.&quot;));
</code></pre>
<ul>
<li>예외 전환은 <strong>예외를 적절한 예외로 전환하여 자신을 호출할 쪽으로 던져버리는 방법</strong>을 뜻한다.</li>
</ul>
<hr />
<h4 id="checked-exception과-unchecked-exception의-차이를-설명해주세요">Checked Exception과 Unchecked Exception의 차이를 설명해주세요.</h4>
<p>Checked Exception은 컴파일 시점에 강제로 처리해야 하는 복구 가능한 예외이고, Unchecked Exception은 런타임에 발생하는 프로그래밍 오류 성격의 예외로 강제되지 않습니다. Spring에서는 Checked는 기본적으로 롤백하지 않고, Unchecked는 롤백합니다.
예외처리의 방법으로는 예외 복구(try-catch), 예외 처리 회피(throws), 예외 전환(throw new)이 있습니다.</p>