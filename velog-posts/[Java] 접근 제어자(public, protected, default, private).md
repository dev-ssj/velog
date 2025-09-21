<h1 id="💡접근-제어자">💡접근 제어자</h1>
<p>자바에는 4가지 접근 제어자가 있다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/c4b82920-ab61-4aa8-9637-d43b32374b3d/image.png" /></p>
<ul>
<li>** public** : 어느 곳에서나 접근 가능 -&gt; 접근 제약이 없다</li>
<li><strong>protected</strong> : 동일 패키지 내에 존재하거나, 해당 클래스를 상속받은 외부 패키지의 클래스에서 접근이 가능</li>
<li><strong>default</strong> : 기본 접근 제한자, 동일한 패키지 내에서만 접근 가능</li>
<li><strong>private</strong> : 자기 자신의 클래스 내에서만 접근 가능 -&gt; 같은 클래스 내에서만 접근 가능</li>
</ul>
<h2 id="✅접근-제어자-사용-범위">✅접근 제어자 사용 범위</h2>
<ul>
<li><strong>클래스</strong> : public, default만 사용 가능</li>
<li><strong>메소드</strong> : 모든 접근 제어자 사용 가능</li>
<li><strong>인스턴스 변수</strong> : 모든 접근 제어자 사용 가능</li>
<li><strong>지역 변수</strong> : 사용 불가</li>
</ul>
<hr />
<h2 id="✅정리">✅정리</h2>
<table>
<thead>
<tr>
<th>대상 \ 접근</th>
<th><code>private</code></th>
<th><em>(package-private)</em> (기본)</th>
<th><code>protected</code></th>
<th><code>public</code></th>
</tr>
</thead>
<tbody><tr>
<td>같은 클래스</td>
<td>✅</td>
<td>✅</td>
<td>✅</td>
<td>✅</td>
</tr>
<tr>
<td>같은 패키지(타 클래스)</td>
<td>❌</td>
<td>✅</td>
<td>✅</td>
<td>✅</td>
</tr>
<tr>
<td>다른 패키지의 하위클래스</td>
<td>❌</td>
<td>❌</td>
<td>✅</td>
<td>✅</td>
</tr>
<tr>
<td>다른 패키지의 비하위클래스</td>
<td>❌</td>
<td>❌</td>
<td>❌</td>
<td>✅</td>
</tr>
<tr>
<td><strong>적용 가능 대상</strong></td>
<td>필드/메서드/생성자/중첩클래스</td>
<td><strong>최상위 클래스/인터페이스 가능</strong>, 그 외 전부</td>
<td>필드/메서드/생성자/중첩클래스</td>
<td><strong>최상위 클래스/인터페이스 가능</strong>, 그 외 전부</td>
</tr>
</tbody></table>
<hr />
<h4 id="java의-접근-제어자-4가지와-각각의-범위를-설명해주세요">Java의 접근 제어자 4가지와 각각의 범위를 설명해주세요.</h4>
<p>자바의 접근 제어자로는 private, defualt, protected, public 4가지가 있습니다.
private는 같은 클래스 내부, defualt는 같은 패키지 내부, protected는 동일 패키지 또는 상속받은 클래스, public은 모든 곳에서 접근이 가능합니다.</p>