<h1 id="💡jpa-연관관계">💡JPA 연관관계</h1>
<h2 id="1-11-관계onetoone">1. 1:1 관계(OneToOne)</h2>
<h3 id="🔍어노테이션">🔍어노테이션</h3>
<blockquote>
<p>*<em>@OneToOne(mappedBy = &quot;member&quot;) *</em></p>
</blockquote>
<h3 id="📍코드-예시">📍코드 예시</h3>
<pre><code class="language-java">@Entity
Public Las Member {
    @Id @GeneratedValue
    private Long id;

    @OneToOne(mappedBy = &quot;member&quot;) 
    private IDCard idCard;
}

@Entity
Public Las IDCard { 
    @Id @GeneratedValue
    private Long id;

    @OneToOne
    @JoinColumn(name = &quot;member_id&quot;) 
    private Member member;
}
</code></pre>
<ul>
<li>mappedBy 값은 <strong>테이블명이 아닌 엔티티클래스 명</strong>을 써야한다.</li>
<li>mappedBy가 있는 곳은 연관관계의 주인이 아니다.</li>
<li>@JoinColumn : <strong>연관관계의 주인</strong>. FK가 생긴다.</li>
<li>@JoinColumn의 name은 엔티티 클래스의 필드명이 아닌 *<em>테이블의 필드명을 써야한다.
*</em></li>
</ul>
<hr />
<h2 id="2-1n-관계onetomany---단방향">2. 1:N 관계(OneToMany) - 단방향</h2>
<h3 id="🔍어노테이션-1">🔍어노테이션</h3>
<blockquote>
<p><strong>@OneToMany(name=”member_id”)</strong></p>
</blockquote>
<h3 id="📍코드-예시-1">📍코드 예시</h3>
<pre><code class="language-java">@Entity
public class Member {
    @Id @GeneratedValue
    private Long id;

    @OneToMany //한 명의 회원이 여러 개의 주문을 할 수 있음
    @JoinColumn(name = &quot;member_id&quot;)
    private List&lt;Order&gt; orders = new ArrayList&lt;&gt;();
}</code></pre>
<ul>
<li>단방향은 한쪽 엔티티에서만 참조 가능</li>
<li>Order에서 member를 알 수 없어서 객체 접근 불가능(order.getMember() -&gt; 불가능)</li>
</ul>
<hr />
<h2 id="3-1n관계onetomany-manytoone---양방향">3. 1:N관계(OneToMany, ManyToOne) - 양방향</h2>
<h3 id="🔍어노테이션-2">🔍어노테이션</h3>
<blockquote>
<p>** @OneToMany(mappedBy = “member”)
@ManyToOne(fetch=FetchType.Lazy)**</p>
</blockquote>
<h3 id="📍코드-예시-2">📍코드 예시</h3>
<pre><code class="language-java">@Entity
public class Member {
    @Id @GeneratedValue
    private Long id;

    //한명의 회원이 여러 개의 주문을 할 수 있다.
    @OneToMany(mappedBy = &quot;member&quot;) ) 
    private List&lt;Order&gt; orders = new ArrayList&lt;&gt;();
}

@Entity
public class Order {
    @Id @GeneratedValue
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    //FK는 항상 N쪽에
    @JoinColumn(name = &quot;member_id&quot;)   
    private Member member;
}</code></pre>
<ul>
<li>1:N 양방향 관계에서 <strong>FK는 항상 N쪽에</strong> -&gt; @JoinColumn(name = &quot;member_id&quot;) </li>
</ul>
<hr />
<h1 id="💡cascadetype">💡CascadeType</h1>
<h2 id="✅cascadetype이란">✅CascadeType이란?</h2>
<blockquote>
<p>JPA에서 엔티티 간 연관관계에서 특<strong>정 작업(저장, 삭제 등)을 전파할지 여부를 설정하는 열거형</strong>
부모-&gt; 자식 관계에서 사용</p>
</blockquote>
<hr />
<h2 id="🔍cascadetype-옵션">🔍CascadeType 옵션</h2>
<ol>
<li>ALL : 모든 작업(저장, 수정, 삭제 등) 전파</li>
<li>PERSIST : 부모 엔티티 저장시 자식 엔티티도 함께 저장</li>
<li>MERGE : 부모 엔티티 병합 시 자식 엔티티도 함께 병합</li>
<li>REMOVE : 부모 엔티티 삭제 시 자식 엔티티도 함께 삭제</li>
<li>REFRESH : 부모 엔티티 새로고침 시 자식 엔티티로 함께 새로 고침</li>
<li>DETACH : 부모 엔티티 detach 시 자식 엔티티도 detach됨</li>
</ol>
<hr />
<h2 id="📍사용-예시">📍사용 예시</h2>
<pre><code class="language-java">@OneToMany(mappedBy = &quot;board&quot;, cascade = CascadeType.ALL, orphanRemoval = true) 
private List&lt;Comment&gt; comment = new ArrayList&lt;&gt;();</code></pre>
<ul>
<li>CacadeType.ALL : 부모(board)가 삭제되면 자식(Comment)도 함께 삭제됨</li>
<li>orphanRemoval=true : 부모가 없는 고아 객체도 같이 삭제됨</li>
</ul>
<hr />
<h1 id="💡auditing">💡Auditing</h1>
<h2 id="✅auditing이란">✅Auditing이란?</h2>
<blockquote>
<p>created_date, update_date 등 여러 테이블에서 반복해서 사용되는 필드들은 매번 엔티티 작성 시 추가하기 번거롭다.
이 때, Auditing을 사용하면 반복되는 필드들을 모아서 따로 설정해줄 수 있다.</p>
</blockquote>
<h2 id="✅auditing-사용하기">✅Auditing 사용하기</h2>
<h3 id="1-abstract-class-basetime-클래스-생성">1. abstract class BaseTime 클래스 생성</h3>
<pre><code class="language-java">@EntityListeners(AuditingEntityListener.class)

@MappedSuperclass
public abstract class BaseTime {

    //엔티티가 저잘될때 자동으로 시간을 기록
    @CreatedDate
    private LocalDateTime created_date;

    //수정될 때마다 기록
    @LastModifiedDate
    private LocalDateTime updated_date;
}</code></pre>
<ul>
<li>공통 필드들이 모여있는 클래스</li>
<li>@MappedSuperclass : JPA 엔티티의 생성/수정 시점을 자동으로 기록하게 해주는 어노테이션</li>
</ul>
<h3 id="2-basetime을-사용할-클래스에-basetime을-extends">2. BaseTime을 사용할 클래스에 BaseTime을 extends</h3>
<pre><code class="language-java">public class Board extends BaseTime(){...}</code></pre>
<h3 id="3-projectapplication에-enablejpaauditiong-추가">3. ProjectApplication에 @EnableJpaAuditiong 추가</h3>
<pre><code class="language-java">@SpringBootApplication
@EnableJpaAuditing
public class BackendProjectApplication {
...
}</code></pre>
<hr />
<h1 id="💡페이징">💡페이징</h1>
<blockquote>
<p>Spring Data JPA에서는 페이징 처리시 Pageable 인터페이스를 사용하면 매우 쉽게 페이징 처리를 구현할 수 있다.</p>
</blockquote>
<h2 id="✅페이징의-기본-구조-이해">✅페이징의 기본 구조 이해</h2>
<table>
<thead>
<tr>
<th>개념</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><code>Pageable</code></td>
<td>페이징 처리에 필요한 요청 정보 (page 번호, size, sort 등)를 담는 인터페이스</td>
</tr>
<tr>
<td><code>PageRequest.of(page, size, sort)</code></td>
<td><code>Pageable</code>의 구현체</td>
</tr>
<tr>
<td><code>Page&lt;T&gt;</code></td>
<td>JPA가 반환하는 결과 객체로, <code>List&lt;T&gt;</code>뿐만 아니라 전체 페이지 수, 총 개수, 현재 페이지 등도 포함</td>
</tr>
</tbody></table>
<hr />
<h2 id="🔍페이징-주요-메서드">🔍페이징 주요 메서드</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/0a7dea87-213a-46f2-bce6-07cd35bbc27f/image.png" /></p>
<hr />
<h2 id="📍예시">📍예시</h2>
<h3 id="boardrepository">BoardRepository</h3>
<pre><code class="language-java">@Query(&quot;SELECT new com.example.backendproject.board.dto.BoardDTO(&quot; +
       &quot;b.id, b.title, b.content, b.user.userProfile.username, b.user.id, b.created_date, b.updated_date) &quot; +
       &quot;FROM Board b&quot;)
Page&lt;BoardDTO&gt; findAllPaging(Pageable pageable);</code></pre>
<ul>
<li><code>Board b</code> 엔티티에서 데이터 조회</li>
<li>필요한 필드만 뽑아서 <code>BoardDTO</code> 생성자로 매핑(엔티티 전체를 로딩 하지 않음 -&gt; <strong>성능 면에서 우수</strong>)</li>
<li><code>Pageable</code> 파라미터를 사용해서 자동으로 페이징</li>
<li>결과는 <code>Page&lt;BoardDTO&gt;</code>로 반환된다</li>
</ul>
<h3 id="boardservice">BoardService</h3>
<pre><code class="language-java"> public Page&lt;BoardDTO&gt; getBoards(int page, int size) {
        return boardRepository.findAllPaging(PageRequest.of(page, size)); //페이저블에 페이징에대한 정보를 담아서 레포지토리에 전달하는 역할
    }</code></pre>
<ul>
<li>page : 페이지 번호(기본값 : 0)</li>
<li>size : 한페이지 당 불러올 데이터 갯수</li>
</ul>
<h3 id="boardcontroller">BoardController</h3>
<pre><code class="language-java"> @GetMapping
    public Page&lt;BoardDTO&gt; getBoards(
            @RequestParam(defaultValue = &quot;0&quot;) int page,
            @RequestParam(defaultValue = &quot;10&quot;) int size
    ) {
        return boardService.getBoards(page, size);
    }</code></pre>
<ul>
<li>1페이지부터 데이터 10개씩 가져온다.</li>
</ul>
<hr />
<h2 id="✅페이징--정렬">✅페이징 + 정렬</h2>
<h3 id="⚠️정렬은-pageable로-넘겨야-적용이-된다">⚠️정렬은 Pageable로 넘겨야 적용이 된다.</h3>
<pre><code class="language-java">//이렇게 하면 Pageable의 Sort 무시됨.
@Query(&quot;SELECT ... FROM Board b ORDER BY b.title DESC&quot;)</code></pre>
<hr />
<h3 id="📍예시-1">📍예시</h3>
<pre><code class="language-java">return boardRepository.findAllPaging(
    PageRequest.of(page, size, Sort.by(&quot;created_date&quot;).descending())
);</code></pre>
<ul>
<li>위 코드는 내부적으로 아래와 같은 SQL 코드를 작성한다.<pre><code class="language-sql">SELECT ... FROM board
ORDER BY created_date DESC
LIMIT size OFFSET page</code></pre>
</li>
</ul>