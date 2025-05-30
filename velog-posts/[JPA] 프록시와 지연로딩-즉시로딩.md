<blockquote>
<p>Team과 Member가 1:N으로 매핑된다고 가정해보자.
이때 Member를 조회할 때 Team도 함께 조회해야할까?
비즈니스 로직에서 Team을 필요로 하지 않을 때에는 꼭 TEAM도 같이 조회할 필요가 없다.
➡️ JPA는 이런 낭비(성능저하)를 하지 않기 위해 <code>지연로딩</code>과 <code>프록시</code>라는 개념으로 해결한다.</p>
</blockquote>
<h1 id="💡프록시proxy">💡프록시(Proxy)</h1>
<blockquote>
<p>간단히 말해서 <strong>가짜 객체</strong>를 의미한다.</p>
</blockquote>
<h2 id="📗프록시-기초">📗프록시 기초</h2>
<ul>
<li>em.find() : 데이터베이스를 통해 <code>실제 엔티티 객체 조회</code></li>
<li>em.getReference() : 데이터베이스 조회를 미루는 <code>가짜(프록시) 엔티티 객체 조회</code><img src="https://velog.velcdn.com/images/dev_ssj/post/3f124b7f-00f5-41b4-95ed-16e5a3838d14/image.png" />

</li>
</ul>
<h2 id="📗프록시-객체의-초기화">📗프록시 객체의 초기화</h2>
<blockquote>
<p>** DB에 직접 조회해서 값을 가져와 entity를 만들어 내는 과정**</p>
</blockquote>
<img src="https://velog.velcdn.com/images/dev_ssj/post/c64876c2-d499-4ebe-822f-5ed5cf7abd39/image.png" />

<blockquote>
<p>(1) Member member = em.getReference(...); -&gt; <code>프록시 객체 호출</code>
(2) member.getName(); -&gt; 영속성 컨텍스트에 <code>초기화</code> 요청
(3) 영속성 컨텍스트는 DB조회해서 실제 엔티티 객체 생성<br />(4) target에 실제 엔티티 객체를 연결시켜준다
➡️ 즉, target의 getName()을 통해서 멤버의 getName()이 반환됨
(5) 이 후, member의 getName()을 하게되면 기존에 조회된 것이 있으므로 target에서 name을 조회한다.</p>
</blockquote>
<h2 id="📗프록시-특징">📗프록시 특징</h2>
<img src="https://velog.velcdn.com/images/dev_ssj/post/1cf08c08-45cf-429f-a6e3-c199d1742df1/image.png" />

<ul>
<li><code>실제 클래스를 상속</code>받아서 만들어진다</li>
<li>실제 클래스와 겉모양이 같다</li>
</ul>
<img src="https://velog.velcdn.com/images/dev_ssj/post/ab0cf216-535f-4a2f-862a-e69c53256f98/image.png" />

<ul>
<li>프록시 객체는 <code>실제 객체의 참조(target)</code>를 보관</li>
<li>프록시 객체를 호출하면 프록시 객체는 실제 객체의 메소드 호출</li>
<li>프록시 객체는 처음 사용할 때 <code>한번만 초기화</code>된다</li>
<li>프록시 객체를 초기화 할 때, 프록시 객체가 실제 엔티티로 바뀌는 것이 아니다.<ul>
<li>초기화가 되면 프록시 객체를 통해 실제 엔티티에 접근 가능한 것</li>
</ul>
</li>
<li>프록시 객체는 원본 엔티티를 상속 받는다.<ul>
<li>따라서 타입 체크 시 주의해야 함 ( == 비교 실패. 대신 instance of 사용)</li>
</ul>
</li>
<li>영속성 컨텍스트에 찾는 엔티티가 이미 있으면 <code>em.getReference()</code>를 호출해도 실제 엔티티를 반환한다. </li>
<li>영속성 컨텍스트의 도움을 받을 수 없는 준영속 상태일때, 프록시를 초기화하면 문제가 발생한다.</li>
</ul>
<h2 id="📗프록시-확인">📗프록시 확인</h2>
<ul>
<li>프록시 인스턴시의 초기화 여부 확인<ul>
<li><code>PersistenceUnitUtil.isLoaded(Object entity)</code></li>
</ul>
</li>
<li>프록시 클래스 확인 방법<ul>
<li><code>entity.getClass().getName</code> 출력</li>
</ul>
</li>
<li>프록시 강제 초기화<ul>
<li><code>org.hibernate.Hibernate.initalize(entity);</code></li>
</ul>
</li>
</ul>
<blockquote>
<p>📌<strong>참고</strong>
JPA 표준은 강제 초기화 없다
 강제 호출 : member.getName()</p>
</blockquote>
<h1 id="💡지연로딩과-즉시로딩">💡지연로딩과 즉시로딩</h1>
<h2 id="📗지연로딩lazy">📗지연로딩(LAZY)</h2>
<blockquote>
<p><strong>필요한 시점에 연관된 객체의 데이터를 불러오는 것</strong>
➡️ <strong>(fetch = FetchType.LAZY)</strong></p>
</blockquote>
<pre><code class="language-java">@Entity
public class Member {
    @Id @GeneratedValue
    private Long id;

    @Column(name = &quot;USERNAME&quot;)
    private String username;

    @ManyToOne(fetch = FetchType.LAZY)  //패치타입 LAZY : 프록시로 가져옴
    @JoinColumn(name =&quot;TEAM_ID&quot;)
    private Team team;
    ...
}</code></pre>
<blockquote>
<ul>
<li>프록시 객체로 조회한다
➡️지연로딩을 의미</li>
</ul>
</blockquote>
<ul>
<li>Member만 DB에서 조회하고 team은 프록시로 조회</li>
</ul>
<pre><code class="language-java">Team team1 = new Team();
team1.setName(team1);
em.persist(team1);

Member ember1 = new Member();
member1.setUsername(&quot;member1&quot;);
member1.setTeam(team);
em.persist(member1);

Member m =em.find(Member.class, member.getId());
//team의 속성 조회
System.out.println(&quot;m = &quot; + m.getTeam().getName());</code></pre>
<img src="https://velog.velcdn.com/images/dev_ssj/post/1f1694ec-53ca-47ba-9f6e-2bb8f6d619f1/image.png" />

<blockquote>
<ul>
<li>m에 있는 team을 가져올 때 프록시를 가져온다
➡️<code>직접적으로 team을 사용하지 않았을 때</code></li>
</ul>
</blockquote>
<ul>
<li>team내에 속성을 조회하게 되면 이 시점에서 DB에서 쿼리로 조회한다.
➡️<code>team의 필드를 사용할때 실제 쿼리문이 나간다.</code></li>
</ul>
<h2 id="📗즉시로딩eager">📗즉시로딩(EAGER)</h2>
<blockquote>
<p>** 데이터를 조회할 때, 연관된 모든 객체의 데이터까지 한 번에 불러오는 것 **
➡️** (fetch = FetchType.EAGER)**</p>
</blockquote>
<pre><code class="language-java">@Entity
public class Member extends BaseEntity{
    @Id @GeneratedValue

    private Long id;

    @Column(name = &quot;USERNAME&quot;)
    private String username;

    //패치타입 EAGER : 프록시X, 바로 가져옴
    @ManyToOne(fetch = FetchType.EAGER) 
    @JoinColumn(name =&quot;TEAM_ID&quot;)
    private Team team;</code></pre>
<pre><code class="language-java">Team team1 = new Team();
team1.setName(team1);
em.persist(team1);

Member ember1 = new Member();
member1.setUsername(&quot;member1&quot;);
member1.setTeam(team);
em.persist(member1);

Member m =em.find(Member.class, member.getId());
//team의 속성 조회
System.out.println(&quot;m = &quot; + m.getTeam().getName());</code></pre>
<img src="https://velog.velcdn.com/images/dev_ssj/post/97f384f4-4fc9-4030-a318-6ad1c2109ce7/image.png" />

<blockquote>
<ul>
<li>team의 속성 조회(m.getTeam().getName())시에 team에 대한 조회 쿼리가 나가는 게 아니라 <code>member를 조회할 때 team도 한꺼번에 같이 조회</code>한다.</li>
</ul>
</blockquote>
<h2 id="📗프록시와-즉시로딩-주의">📗프록시와 즉시로딩 주의</h2>
<ul>
<li><span style="color: red;"><strong>가급적 지연로딩만 사용(특히나 실무에서!)</strong></span></li>
<li>즉시로딩을 적용하면 예상치 못한 SQL이 발생한다.</li>
<li>즉시로딩은 JPQL에서 <code>N+1 문제</code>를 일으킨다.<ul>
<li>해결방안1 : JPQL에서 <code>fetchJoin</code>을 사용한다.</li>
<li>해결방안2 : <code>@EntityGraph</code>를 사용한다.</li>
<li>해결방안3 : <code>batchSize</code>를 설정한다.</li>
</ul>
</li>
<li><code>@ManyToOne</code>, <code>@OneToOne</code>은 기본이 즉시로딩</li>
<li><blockquote>
<p>LAZY로 설정</p>
</blockquote>
</li>
<li><code>@OneToMaany</code>, <code>@ManyToMany</code>는 기본이 지연로딩</li>
</ul>
<h2 id="📗지연로딩-활용실무">📗지연로딩 활용(실무)</h2>
<ul>
<li><span style="color: red;"> <strong>모든 연관관계에 지연로딩을 사용해라</strong></span></li>
<li><strong>실무에서 즉시로딩을 사용하지 마라</strong></li>
<li>JPQL fetch 조인이나 엔티티 그래프 기능을 사용해라</li>
<li>즉시로딩은 상상치 못한 쿼리가 나가기 때문에 사용하지 않아야 한다!</li>
</ul>