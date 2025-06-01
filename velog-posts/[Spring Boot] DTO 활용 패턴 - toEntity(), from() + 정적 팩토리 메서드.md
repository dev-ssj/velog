<h2 id="💡-toentity와-from의-사용이유와-차이점">💡 toEntity()와 from()의 사용이유와 차이점</h2>
<p>toEntity()와 from() 둘다 <code>Entity ↔ Dto</code> 변환에서 자주 사용하는 메서드이다.
Dto로 변환하느냐, Entity로 변환하느냐에 따라 toEntity()를 사용할지, from()을 사용할지가 나뉜다.</p>
<ul>
<li><code>toEntity()</code>  → DTO를 Entity로 변환</li>
<li><code>from()</code> → Entity를 DTO로 변환</li>
</ul>
<blockquote>
<p>❓그런데 왜 <code>toEntity()</code>는 static이 아니고 <code>from()</code>은 static 일까?</p>
</blockquote>
<hr />
<h2 id="✅왜-from은-static일까">✅왜 from()은 static일까?</h2>
<table>
<thead>
<tr>
<th>메서드</th>
<th>용도</th>
<th>메서드 타입</th>
<th>이유</th>
</tr>
</thead>
<tbody><tr>
<td>toEntity()</td>
<td>DTO → Entity</td>
<td><strong>인스턴스 메서드</strong></td>
<td>DTO의 <strong>자기 필드(this)</strong>를 사용하니까</td>
</tr>
<tr>
<td>from()</td>
<td>Entity → DTO</td>
<td><strong>정적 팩토리 메서드(static)</strong></td>
<td>Entity를 외부에서 받아서 처리하니까 <strong>객체 의존이 없음</strong></td>
</tr>
<tr>
<td>---</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>## ✅코드 예시</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>### 📌UserRequestDto : toEntity()</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>```java</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>@Getter</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>public class UserReqestDto{</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>private String name;</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<td>private String email</td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody></table>
<p>   @Builder
    public UserRequestDto(String name, String email) {
        this.name = name;
        this.email = email;
    }</p>
<pre><code>//Entity -&gt; DTO 변환
public User toEntity() {
    return User.builder()
        .name(this.name)
        .email(this.email)
        .build();
}</code></pre><p>}</p>
<p>/*</p>
<ul>
<li>Controller 사용 예시</li>
<li>/
UserRequestDto requestDto = UserRequestDto.builder()
   .name(&quot;홍길동&quot;)
   .email(&quot;<a href="mailto:aaa@aaa.com">aaa@aaa.com</a>&quot;)
   .build();</li>
</ul>
<p>User user = requestDto.toEntity();    //DTO -&gt; Entity로 변환</p>
<pre><code>- `this.name`, `this.email`처럼 자기 자신의 내부필드(**this**)를 쓰기때문에 **객체가 있어야 작동됨** → static ❌
---
### 📌UserResponseDto : from()
```java
@Getter
public class UserResponseDto{
    private Long id;
    private String name;
    private String email;

    @Bulider
    public UserResponseDto(Long id, String name, String email) {
    this.id = id;
    this.name = name;
    this.email = email;
    }

    //Entity -&gt; DTO 변환
    public static UserResponseDto from(User user){
    return UserResponseDto.builder()
        .id(user.getId())
        .name(user.getName())
        .email(user.getEmail())
        .build();
    }
}

/*
* Controller 사용 예시
*/
User user = userRepository.findById(1L).orElseThrow();
UserResponseDto responseDto = UserResponseDto.from(user);</code></pre><ul>
<li><code>User user</code>라는 외부 객체만 받아 쓰므로 <strong>내부상태(this)에 의존 없음</strong> → static ⭕</li>
<li>즉, <code>정적 팩토리 메서드</code>이다.</li>
</ul>
<hr />
<h2 id="💡정적-팩토리-메서드가-뭔데">💡정적 팩토리 메서드가 뭔데?</h2>
<ul>
<li>객체를 생성하는 방식 중 하나로, Java에서 <code>생성자</code> 대안으로 자주 쓰이는 설계 기법</li>
<li>정적(static) 메서드를 통해 객체를 생성해서 반환하는 메서드</li>
</ul>
<blockquote>
<p>➡️즉, <code>new</code> 키워드 대신 클래스 내부의 <code>static 메서드</code>를 통해 인스턴스를 생성하는 방식이다!</p>
</blockquote>
<hr />
<h3 id="생성자-vs-정적-팩토리-메서드">생성자 vs 정적 팩토리 메서드</h3>
<h4 id="📌생성자로-객체-만들기">📌생성자로 객체 만들기</h4>
<pre><code class="language-java">User user = new User(&quot;홍길동&quot;,&quot;aaa@aa.com&quot;);</code></pre>
<h4 id="📌정적-팩토리-메서드로-객체-만들기">📌정적 팩토리 메서드로 객체 만들기</h4>
<pre><code class="language-java">User user = new User.of(&quot;홍길동&quot;,&quot;aaa@aa.com&quot;);</code></pre>
<hr />
<h2 id="✅그래서-왜-쓰는건데">✅그래서 왜 쓰는건데</h2>
<blockquote>
<p>생성자보다 더 유연하고 명확하게 객체 생성 의도를 표현할 수 있기 때문에</p>
</blockquote>
<pre><code class="language-java">// 생성자 방식 (어떤 의미인지 애매)
User user = new User(&quot;홍길동&quot;, &quot;ssj@example.com&quot;);

// 정적 팩토리 방식 (의도가 명확함)
User user = User.withNameAndEmail(&quot;홍길동&quot;, &quot;ssj@example.com&quot;);
</code></pre>
<hr />
<h2 id="💡toentity와-from의-service-사용-예시">💡toEntity()와 from()의 Service 사용 예시</h2>
<h3 id="📌-회원가입-처리">📌 회원가입 처리</h3>
<pre><code class="language-java">public UserResponseDto signup(UserRequestDto request) {
    // 요청 DTO → Entity 
    User user = request.toEntity();

    // 저장 후 Entity → 응답 DTO 
    return UserResponseDto.from(userRepository.save(user));
}</code></pre>
<ul>
<li><code>toEntity()</code><strong>는 Dto 인스턴스가 있으므로 바로 호출</strong></li>
<li><code>from()</code>은 <strong>Entity를 받아서 Dto로 만드는 정적 호출</strong></li>
</ul>
<hr />
<h2 id="✅toentity와-from-비교-정리">✅toEntity()와 from() 비교 정리</h2>
<table>
<thead>
<tr>
<th>항목</th>
<th>toEntity()</th>
<th>from()</th>
</tr>
</thead>
<tbody><tr>
<td>방향</td>
<td>DTO → Entity</td>
<td>Entity → DTO</td>
</tr>
<tr>
<td>위치</td>
<td>RequestDto</td>
<td>ResponseDto</td>
</tr>
<tr>
<td>목적</td>
<td>데이터 저장 전 변환</td>
<td>데이터 응답 전 변환</td>
</tr>
<tr>
<td>선언 형태</td>
<td>인스턴스메서드</td>
<td>public static 메서드</td>
</tr>
<tr>
<td>주 사용처</td>
<td>Controller → Service / 저장 로직</td>
<td>Service → Controller / API 응답</td>
</tr>
</tbody></table>
<blockquote>
<p>📌<strong>기본 설계 패턴</strong></p>
</blockquote>
<ul>
<li>엔티티 → @Builder</li>
<li>RequestDto → @Builder + toEntity()</li>
<li>ResponseDto → @Builder + static from(Entity)</li>
</ul>