<h2 id="💡생성자-자동-생성-어노테이션">💡생성자 자동 생성 어노테이션</h2>
<blockquote>
<p><code>@NoArgsConstructor</code> <code>@AllArgsConstructor</code> <code>@RequiredArgsConstructor</code> 는 Lombok에서 제공하는 생성자 자동 생성 어노테이션이다. 각 어노테이션의 차이점을 알아보겠다.</p>
</blockquote>
<hr />
<h3 id="✅noargsconstructor">✅@NoArgsConstructor</h3>
<ul>
<li><strong>기본 생성자(파라미터가 없는 생성자)</strong>를 자동 생성한다</li>
</ul>
<h4 id="📍예시">📍예시</h4>
<pre><code class="language-java"> @NoArgsConstructor
public class User {
    private String name;
    private int age;
}</code></pre>
<h4 id="📍결과">📍결과</h4>
<pre><code class="language-java">public User() {}</code></pre>
<blockquote>
<p>📌<strong>JPA Entity에서는 필수</strong>이다. → 프록시 생성을 위해 기본생성자 필요</p>
</blockquote>
<hr />
<h3 id="✅allargsconstructor">✅@AllArgsConstructor</h3>
<ul>
<li><strong>모든 필드를 인자로 받는 생성자</strong>를 자동 생성한다</li>
</ul>
<h4 id="📍예시-1">📍예시</h4>
<pre><code class="language-java">@AllArgsConstructor
public class User {
    private String name;
    private int age;
}</code></pre>
<h4 id="📍결과-1">📍결과</h4>
<pre><code class="language-java">public User(String name, int age) {
    this.name = name;
    this.age = age;
}</code></pre>
<blockquote>
<p>📌<strong>DTO나 VO</strong>등에서 자주 사용 된다.</p>
</blockquote>
<hr />
<h3 id="✅requiredargsconstructor">✅@RequiredArgsConstructor</h3>
<ul>
<li><strong>final필드</strong> 또는 <strong>@NonNull 필드</strong>만 파라미터로 받는 생성자 생성<h4 id="📍예시-2">📍예시</h4>
<pre><code class="language-java">@RequiredArgsConstructor
public class UserService {
  private final UserRepository userRepository;
  private String name;
}</code></pre>
<h4 id="📍결과-2">📍결과</h4>
<pre><code class="language-java">public UserService(UserRepository userRepository) {
  this.userRepository = userRepository;
}</code></pre>
<blockquote>
<p>📌스프링에서 <strong>생성자 주입용</strong>으로 가장 자주 사용된다 
→ <code>@Service</code> <code>@Component</code> 클래스에서 주로 사용</p>
</blockquote>
</li>
</ul>
<hr />
<h2 id="📗정리">📗정리</h2>
<table>
<thead>
<tr>
<th>어노테이션</th>
<th>생성자 파라미터</th>
<th>주요 사용처</th>
</tr>
</thead>
<tbody><tr>
<td><code>@NoArgsConstructor</code></td>
<td>없음</td>
<td>JPA Entity, 직렬화 필요 시</td>
</tr>
<tr>
<td><code>@AllArgsConstructor</code></td>
<td>모든 필드</td>
<td>DTO, 테스트용 객체 생성</td>
</tr>
<tr>
<td><code>@RequiredArgsConstructor</code></td>
<td><code>final</code> 또는 <code>@NonNull</code> 필드만 포함</td>
<td>의존성 주입 (DI) 클래스</td>
</tr>
</tbody></table>