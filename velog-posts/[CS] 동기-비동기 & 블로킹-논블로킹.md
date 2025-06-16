<h1 id="💡동기비동기--블로킹논블로킹">💡동기/비동기 &amp; 블로킹/논블로킹</h1>
<blockquote>
<p>동기/비동기와 블로킹/논블로킹은 표현 형태는 비슷해보여도 서로 다른 차원에서 작업의 수행 방식을 설명하는 개념이다.
<span style="color: red;"><strong>동기/비동기</strong></span>는 요청한 작업에 대해 <strong>완료 여부를 신경 써서 작업을 순차적으로 수행할지 아닌지에 대한 관점</strong>이고, <span style="color: red;"><strong>블로킹/논블록킹</strong></span>은 단어 그대로 현재 작업이 <strong>block(차단, 대기) 되느냐 아니냐에 따라 다른 작업을 수행할 수 있는지에 대한 관점</strong>이다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/034c532a-b984-4917-bee3-e2d902fa0a3d/image.png" /></p>
<hr />
<h1 id="💡동기synchronous비동기asynchronous">💡동기(Synchronous)/비동기(Asynchronous)</h1>
<blockquote>
<p>동기(Synchronous)는 <strong>요청한 작업에 대해 완료 여부를 따져 순차대로 처리</strong>하는 것을 뜻하며, 비동기(Asynchronous)는 동기와 반대로 <strong>요청한 작업에 대해 완료 여부를 따지지 않고 자신의 다음 작업을 수행</strong>하는 것을 뜻한다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/8f8ee4c9-e1f8-4055-a49f-a82d079b6997/image.png" /></p>
<hr />
<h2 id="✅동기synchronous">✅동기(Synchronous)</h2>
<h3 id="🔍동기란">🔍동기란?</h3>
<blockquote>
<p>작업을 <strong>순차적으로 처리</strong>하고, 이전 작업으로 끝나야 다음 작업이 실행되는 방식</p>
</blockquote>
<h3 id="🎯동기의-특징">🎯동기의 특징</h3>
<ul>
<li>하나의 작업이 끝날 때까지** 다음 작업 대기**</li>
<li>코드가 위에서 아래로 차례대로 실행(<strong>순차적</strong>으로 진행)</li>
<li>구현은 단순하지만 <strong>대기 시간이 길면 비효율적</strong></li>
</ul>
<h3 id="📍동기의-예시">📍동기의 예시</h3>
<pre><code class="language-java">System.out.println(&quot;1번 작업 시작&quot;);
Thread.sleep(2000);  // 2초 대기
System.out.println(&quot;2번 작업 완료&quot;);</code></pre>
<ul>
<li>2번 작업은 1번 작업이 끝나야 실행됨</li>
</ul>
<h3 id="⭕동기의-장점">⭕동기의 장점</h3>
<ul>
<li>결과값을 바로 사용할 수 있어 간단하고 직관적</li>
<li>코드가 순차적으로 실행됨 -&gt; 디버깅이 상대적으로 쉬움</li>
<li>서버와 클라이언트 사이의 요청과 응답이 일치하므로 데이터 일관성 유지가 용이</li>
</ul>
<h3 id="❌동기의-단점">❌동기의 단점</h3>
<ul>
<li>요청을 보낸 후 응답이 올 때까지 대기해야 하므로 다른 작업을 수행하지 못함 -&gt; <strong>시스템 전체 성능 저하 가능성</strong></li>
<li>서버에서 응답이 오지 않으면 클라이언트는 영원히 기다려야 할 수도 있음 -&gt; <strong>데드락(deadlock)</strong></li>
<li>서버 부하가 많을 경우 대기 시간이 길어짐</li>
</ul>
<hr />
<h2 id="✅비동기asynchronous">✅비동기(Asynchronous)</h2>
<h3 id="🔍비동기란">🔍비동기란?</h3>
<blockquote>
<p>작업을 병렬적으로 처리할 수 있고, <strong>어떤 작업이 끝날 때까지 기다리지 않고 다음 작업을 진행하는 방식</strong></p>
</blockquote>
<h3 id="🎯비동기의-특징">🎯비동기의 특징</h3>
<ul>
<li>요청을 보낸 후 기다리지 않고 바로 다음 작업 진행</li>
<li>작업이 끝나면 콜백/이벤트/결과를 <strong>비동기적으로 통지</strong></li>
<li><strong>성능 향상, 동시성 처리, 응답성 개선</strong>에 유리</li>
</ul>
<h3 id="📍비동기의-예시">📍비동기의 예시</h3>
<pre><code class="language-java">System.out.println(&quot;1번 작업 시작&quot;);

new Thread(() -&gt; {
    try {
        Thread.sleep(2000);
        System.out.println(&quot;2번 작업 완료&quot;);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}).start();

System.out.println(&quot;메인 스레드 종료&quot;);</code></pre>
<ul>
<li>2번 작업은 별도의 스레드에서 처리되며, 메인 스레드는 기다리지 않고 종료됨</li>
</ul>
<h3 id="⭕비동기의-장점">⭕비동기의 장점</h3>
<ul>
<li>요청과 응답이 분리되어 있음 -&gt; 다른 작업을 수행하면서도 <strong>여러 개의 요청 처리 가능</strong></li>
<li>대기하지 않고 다음 작업을 수행 -&gt; 전체적인 <strong>성능 향상</strong></li>
<li>서버 부하가 많을 때도 대기 시간이 상대적으로 짧음</li>
</ul>
<h3 id="❌비동기의-단점">❌비동기의 단점</h3>
<ul>
<li>코드가 복잡해지며, 비동기 처리 방식에 대한 이해 필요</li>
<li>순서를 보장하지 않음 -&gt; 결과값을 처리하기 전에 다음 작업을 수행해버리는 문제 발생</li>
</ul>
<hr />
<h2 id="☑️비동기-vs-동기">☑️비동기 vs 동기</h2>
<table>
<thead>
<tr>
<th>구분</th>
<th>동기(Sync)</th>
<th>비동기(Async)</th>
</tr>
</thead>
<tbody><tr>
<td>처리 방식</td>
<td>요청 → 응답을 받을 때까지 기다림</td>
<td>요청 → 응답을 기다리지 않고 바로 다음 실행</td>
</tr>
<tr>
<td>흐름 제어</td>
<td>순차적</td>
<td>병렬적, 분기 처리</td>
</tr>
<tr>
<td>장점</td>
<td>구현이 단순함</td>
<td>자원 활용이 효율적, 높은 성능 가능</td>
</tr>
<tr>
<td>단점</td>
<td>느림, 자원 낭비 가능</td>
<td>복잡한 흐름 제어 필요</td>
</tr>
</tbody></table>
<hr />
<h1 id="💡블로킹blocking논블로킹non-blocking">💡블로킹(Blocking)/논블로킹(Non-Blocking)</h1>
<blockquote>
<p>블로킹과 논블로킹은 단어에서 알 수 있듯이 다른 요청의 작업을 처리하기 위해 <strong>현재 작업을 block(차단, 대기) 하냐 안하냐의 유무를 나타내는 프로세스의 실행 방식</strong>이다.
블로킹은 자신의 작업을 진행하다가 다른 주체의 작업이 시작되면 <strong>다른 작업이 끝날 때까지 기다렸다가 자신의 작업을 시작하는 것</strong>이고, 논블로킹은 <strong>다른 주체의 작업에 관련없이 자신의 작업을 하는 것</strong>이다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/d9fe2cc4-723f-481d-9954-bc6753548499/image.png" /></p>
<hr />
<h2 id="✅블로킹blocking">✅블로킹(Blocking)</h2>
<h3 id="🔍블로킹이란">🔍블로킹이란?</h3>
<blockquote>
<p>자신의 작업을 진행하다가, 다른 작업을 호출했을 때 제어권이 넘어가서 <strong>다른 작업이 끝나고 제어권이 돌아오면 작업을 시작</strong>하는 것이다.
요청에 대한 결과가 처리될 때까지 결과를 기다리고, 결과가 돌아올 때까지 자신의 작업을 수행할 수 없다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/2eca1b30-ab9d-421d-a678-8df7a554a5c1/image.png" /></p>
<ol>
<li>A함수가 B함수를 호출하면 B에게 제어권을 넘김</li>
<li>제어권을 넘겨받은 B는 함수를 실행하고, A는 B에게 제어권을 넘겼기 때문에 함수 실행을 잠시 멈춤(block)</li>
<li>B함수는 실행이 끝나면 자신을 호출한 A에게 제어권을 돌려줌
➡️<strong>피호출자로부터 호출자에게 제어권이 돌아오면 작업을 다시 시작할 수 있다.</strong></li>
</ol>
<h3 id="🎯블로킹의-특징">🎯블로킹의 특징</h3>
<ul>
<li>구현이 <strong>단순하고 직관적</strong></li>
<li>적은 요청 처리에는 안정적이지만, 다수의 동시 요청 처리에 많은 스레드가 필요</li>
</ul>
<h3 id="⭕블로킹의-장점">⭕블로킹의 장점</h3>
<ul>
<li>결과값을 반환할 때까지 기다림 -&gt; 결과값을 활용하여 작업 처리 가능</li>
<li>코드가 <strong>간단하고 직관적</strong></li>
<li>순차 코드 작성 가능 -&gt; <strong>디버깅 용이</strong></li>
</ul>
<h3 id="❌블로킹의-단점">❌블로킹의 단점</h3>
<ul>
<li>스레드가 작업 대기 상태일 때 자원 낭비 -&gt; <strong>시스템 성능 저하</strong></li>
<li>동시성 확장 어려움(스레드 수 증가 = 시스텀 부하)</li>
</ul>
<hr />
<h2 id="✅논블로킹non-blocking">✅논블로킹(Non-Blocking)</h2>
<h3 id="🔍논블로킹이란">🔍논블로킹이란?</h3>
<blockquote>
<p>자신의 작업을 진행하다가 다른 작업을 호출하더라도 <strong>제어권은 호출자가 가지고 있어 기존 작업을 계속 수행</strong>할 수 있다. 
피 호출자는 멀티스레드나 콜백함수를 이용해 작업을 각각의 스레드가 제어권을 가지고 따로 수행한다.
논블로킹 방식에서는 결과값을 바로 사용할 수 없기 때문에, 비동기<strong>(Asynchronous) 방식과 결합하여 작업이 완료될 때 호출되는 콜백 함수(callback function)를 이용</strong>하여 결과값을 처리하며, 콜백 함수는 비동기 함수가 완료된 후 자동으로 호출되는 함수이다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/12982bb9-a289-41fd-ac25-88521b46873e/image.png" /></p>
<ol>
<li>A함수가 B함수를 호출하면 B함수는 실행되지만, <strong>제어권은 A함수가 그대로 가지고 있음</strong></li>
<li>A함수는 계속 제어권을 가지고 있기 때문에 B함수를 호출한 이후에도 자신의 코드를 계속 실행함</li>
</ol>
<blockquote>
<p>➡️<strong>블로킹과 마찬가지로 호출할 때 제어권을 넘겨주기는 하지만 바로 돌려받으며, 제어권을 바로 돌려받기 때문에 계속해서 다른 작업을 할 수 있다</strong></p>
</blockquote>
<h3 id="🎯논블로킹의-특징">🎯논블로킹의 특징</h3>
<ul>
<li>스레드를 블로킹하지 않음 -&gt; <strong>자원 효율성 높음</strong></li>
<li>구현이 복잡하고, 보통 이벤트 루프 / 콜백 / 리액티브 프로그래밍과 함께 사용됨</li>
<li><strong>대규모 트래픽</strong>같은 많은 요청 처리에 최적화</li>
</ul>
<h3 id="⭕논블로킹의-장점">⭕논블로킹의 장점</h3>
<ul>
<li>대기하지 않고 다음 작업 수행 -&gt; 전체적인 <strong>성능 향상</strong></li>
<li>서버 부하가 많을 때도 <strong>대기 시간이 상대적으로 짧음</strong></li>
<li>스레드를 효율적으로 사용 -&gt; <strong>대규모 트래픽</strong> 대응 가능</li>
<li><strong>비동기 처리와 결합</strong>하면 성능 개선 가능</li>
</ul>
<h3 id="❌논블로킹의-단점">❌논블로킹의 단점</h3>
<ul>
<li>결과값을 바로 사용할 수 없음 -&gt; <strong>복잡한 구현(콜백지옥, 상태 관리 필요)</strong></li>
<li>작업 순서가 보장되지 않음 -&gt; 일관성 있는 처리 보장 어려움</li>
<li>작업에 대한 콜백 함수 정의 필요 -&gt; <strong>코드 가독성 저하</strong></li>
</ul>
<hr />
<h2 id="☑️블로킹-vs-논블로킹">☑️블로킹 vs 논블로킹</h2>
<table>
<thead>
<tr>
<th>구분</th>
<th>블로킹 I/O</th>
<th>논블로킹 I/O</th>
</tr>
</thead>
<tbody><tr>
<td>제어권 반환</td>
<td>작업 완료 후</td>
<td>바로 반환</td>
</tr>
<tr>
<td>대기 여부</td>
<td>해당 스레드가 작업 끝날 때까지 대기</td>
<td>대기하지 않음</td>
</tr>
<tr>
<td>효율성</td>
<td>낮음 (스레드 수 증가)</td>
<td>높음 (스레드 수 최소화 가능)</td>
</tr>
<tr>
<td>구현 난이도</td>
<td>낮음</td>
<td>높음 (이벤트 처리 또는 리액티브 필요)</td>
</tr>
</tbody></table>
<hr />
<h1 id="💡동기--비동기-블로킹--논블로킹의-조합">💡동기 &amp; 비동기, 블로킹 &amp; 논블로킹의 조합</h1>
<h2 id="1-동기-블로킹sync-blocking">1. 동기 블로킹(Sync-Blocking)</h2>
<blockquote>
<p>제어권이 넘어가며, 순서대로 진행된다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/be017659-a75c-4bfc-90e6-4c97d22c6551/image.png" /></p>
<ul>
<li>A가 B를 호출</li>
<li>B가 제어권을 얻고 B가 작업을 처리</li>
<li>A는 B가 작업을 처리하는 동안 아무것도 하지 않고 결과를 기다림</li>
<li>B의 작업이 끝남과 동시에 A가 제어권을 가져감</li>
<li>A는 B의 결과를 받음</li>
</ul>
<blockquote>
<p>➡️두 개의 작업이 시작과 동시에 끝나는 시간이 일치한다(<strong>Sync</strong>)
한쪽에서 작업이 시작되면 한쪽에서는 작업을 멈추고 기다린다(<strong>Blocking</strong>)
애플리케이션이 DB 쿼리를 날리고 쿼리 결과를 받는다면 이것은 동기 블로킹이 된다.</p>
</blockquote>
<hr />
<h2 id="2-동기-논블로킹sync-nonblocking">2. 동기 논블로킹(Sync-NonBlocking)</h2>
<blockquote>
<p>제어권이 넘어가진 않지만, 순서대로 진행되어야 하기 때문에 계속적으로 작업 완료를 확인한다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/11a9ef43-3964-4a57-831a-539d2988ed3b/image.png" /></p>
<ul>
<li>A가 B를 호출</li>
<li>A는 제어권 유지</li>
<li>A는 다른 작업을 처리하면서 B의 완료 여부 지속적으로 확인</li>
<li>B의 작업이 완료되면 A는 데이터를 회신하고 나머지 작업 진행</li>
</ul>
<blockquote>
<p>➡️두 개 이상의 작업이 시작 시간이나 끝나는 시간이 같아야 하며(<strong>Sync</strong>) 다른 작업을 기다리지 않는다.(<strong>NonBlocking</strong>) 결국에는 <strong>제어권을 잃지 않아도 다른 작업이 끝날때까지는 현재 작업을 마칠 수가 없다.</strong></p>
</blockquote>
<hr />
<h2 id="3-비동기-블로킹async-blocking---거의-사용x">3. 비동기 블로킹(Async-Blocking) - 거의 사용X</h2>
<blockquote>
<p>순서대로 진행되지 않아도 되지만 제어권이 넘어갔기 떄문에 호출자는 더 진행하지 못하므로 대기시간 발생</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/df55d4aa-5f55-48a2-8719-bdd9855edb8d/image.png" /></p>
<ul>
<li>함수는 다른 함수의 리턴 값을 고려하지 않고 동작(비동기)하지만 다른 함수에게 제어권을 넘겨주고 대기한다. (블로킹)</li>
<li>A는 B의 리함수턴값에 신경쓰지 않고, <strong>콜백함수</strong>를 보낸다 (<strong>비동기</strong>)</li>
<li>B의 작업에 관심없음에도 불구하고, A는 B에게 제어권을 넘긴다 (<strong>블로킹</strong>)
.따라서, A는 자신과 관련 없는 B의 작업이 끝날 때까지 기다려야 한다.</li>
</ul>
<blockquote>
<p>➡️다른 작업의 작업 마침여부를 기다리지는 않지만 결국엔 제어권이 작업중인 쪽에 넘어가 있기 때문에 동기-블로킹과 비슷하게 동작한다.
*<em>Sync-Blocking과 비교했을 때 이점이 없기 때문에 거의 사용되지 않는다. *</em></p>
</blockquote>
<hr />
<h2 id="4-비동기-논블로킹async-nonblocking">4. 비동기 논블로킹(Async-NonBlocking)</h2>
<blockquote>
<p>제어권이 넘어가진 않았지만, 순서대로 진행되지 않아도 되기 때문에 호출하고 작업을 하고 있다가 완료시 콜백한다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/5b55b7ed-500d-4e66-bfc9-280fa26a5e10/image.png" /></p>
<ul>
<li>A는 B를 호출</li>
<li>제어권을 B에게 주지 않고, 자신이 계속 가지고 있는다(<strong>논블로킹</strong>)</li>
<li>따라서 B를 호출한 이후에도 멈추지 않고 자신의 코드를 계속 실행한다.</li>
<li>그리고 B를 호출할 때 <strong>콜백함수</strong>도 함께 주는데, B는 자신의 작업이 끝나면 A가 준 콜백함수를 실행한다(<strong>비동기</strong>)</li>
</ul>
<blockquote>
<p>➡️함수가 다른 함수를 호출할 때 <strong>제어권을 주지 않고 자신의 코드를 계속 실행</strong>한다.
함수가 다른 함수를 호출할 때 콜백함수를 함께 줘서 다른 함수는 <strong>자신의 작업을 처리하면 콜백 함수를 실행</strong>한다.</p>
</blockquote>