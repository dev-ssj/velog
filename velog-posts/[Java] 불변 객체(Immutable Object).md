<h1 id="💡불변-객체immutable-object">💡불변 객체(Immutable Object)</h1>
<p><strong>불변 객체란 한 번 생성되면 그 상태가 변하지 않는 객체를 의미</strong>한다. 즉, 한 번 만들어진 객체는 외부에서 값을 변경할 수 없고, 변경이 필요하다면 새로운 객체를 생성해서 반환한다.
자바에서 불변 객체의 예로는 <code>String</code>, <code>Integer</code>와 같은 래퍼클래스와 <code>LocalDate</code>, <code>LocalDateTime</code> 등이 있다.</p>
<ul>
<li>String을 예시로 들어보자.<pre><code class="language-java">String str = &quot;hello&quot;;
str = str + &quot; world&quot;;
</code></pre>
</li>
</ul>
<p>System.out.println(str); // hello world</p>
<p>```
위 코드만 봤을 때는 변수 str이 참조하는 값에 World 문자열을 더해서 str 변수를 업데이트 한것 같지만, 실제 메모리에서는 hello world값을 저장할 영역을 따로 만들고 변수 str을 다시 참조하는 식으로 동작한다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/f15c78c2-2eeb-4270-b238-37e596f1f28a/image.png" /></p>
<hr />
<h2 id="👍불변성의-장점">👍불변성의 장점</h2>
<h3 id="1-thread-safety-스레드-안전성">1. Thread Safety (스레드 안전성)</h3>
<ul>
<li>객체의 상태가 변하지 않기 때문에 여러 스레드가 동시에 접근해도 데이터가 훼손될 염려가 없다. 따라서 <strong>별도의 동기화 처리가 필요 없어 멀티스레드 환경에서 안전</strong>합니다.</li>
</ul>
<h3 id="2-예측-가능한-상태">2. 예측 가능한 상태</h3>
<ul>
<li>객체의 상태가 불변이므로, 개발자가 해당 객체의 상태가 갑자기 바뀌는 것을 걱정할 필요가 없다. 즉, <strong>코드의 안정성과 예측 가능성을 높여 디버깅을 쉽게 만든다.</strong></li>
</ul>
<h3 id="3-캐싱-caching">3. 캐싱 (Caching)</h3>
<ul>
<li>객체의 해시 코드가 생성 후 변하지 않으므로, HashMap이나 HashSet과 같은 자료구조에서 키(Key)로 사용하기에 매우 적합하다. <strong>상태가 변하지 않아 해시 충돌 위험이 줄어듭니다.</strong></li>
</ul>
<h3 id="4-객체의-일관성-보장">4. 객체의 일관성 보장</h3>
<ul>
<li>생성된 이후 상태가 변하지 않으므로 시스템 내에서 <strong>해당 객체를 사용하는 모든 부분에서 일관성을 보장</strong>할 수 있다.</li>
</ul>
<h3 id="5-보안성">5. 보안성</h3>
<ul>
<li>불변객체는 외부에서 상태를 변경할 수 없기 때문에, <strong>객체의 내부 상태를 보호</strong>하는데 유리하다.</li>
</ul>
<h3 id="6-부작용side-effect-최소화">6. 부작용(Side Effect) 최소화</h3>
<ul>
<li>불변 객체는 <strong>동일한 메서드를 호출해도 항상 동일한 결과를 반환</strong>하며, 함수형 프로그래밍에서는 부작용 없는 코드를 선호하기 때문에 불변 객체가 자주 사용된다.</li>
</ul>
<hr />
<h2 id="👎불변성의-단점">👎불변성의 단점</h2>
<h3 id="1-메모리-과도-사용">1. 메모리 과도 사용</h3>
<ul>
<li>불변 객체는 내부 상태가 변경될 때마다 새로운 객체를 생성한다. 이로 인해 <strong>불필요한 메모리 사용을 초래</strong>할 수 있다. -&gt; 해결방법 : StringBuilder, StringBuffer</li>
</ul>
<h3 id="2-성능-저하">2. 성능 저하</h3>
<ul>
<li>상태 변경이 빈번한 경우, <strong>불변 객체를 계속해서 생성하고 가비지 컬렉션이 이를 처리</strong>하게 되므로 성능 저하가 발생할 수 있다.</li>
</ul>
<h3 id="3-구현-복잡성-증가">3. 구현 복잡성 증가</h3>
<ul>
<li>복잡한 연산을 수행하면서 불변성을 유지하려면 객체 설계와 구현이 복잡해 질 수 있다. 모든 필드를 불변으로 유지해야 하고, 내부 상태르 변경하는 메서드를 제공하지 않아야 하므로 설계시 신경쓸 부분이 많아진다.</li>
</ul>
<hr />
<h4 id="immutable-object가-무엇이고-왜-사용하나요">Immutable Object가 무엇이고, 왜 사용하나요?</h4>
<p>Immutable 객체는 생성 후 상태가 바뀌지 않는 객체로, 자바에서는 String을 예시로 들 수 있습니다. 불변 객체는 상태가 변하지 않으므로 재사용에 유리하고 캐시에 적합하니다. 또한, 객체의 일관성을 보장하며 보안성이 높습니다.</p>