<h1 id="💡-단방향-연관관계">💡 단방향 연관관계</h1>
<h2 id="📗단방향-연관관계-설계">📗단방향 연관관계 설계</h2>
<h3 id="1-객체-연관관계-사용">1. 객체 연관관계 사용</h3>
<img src="https://velog.velcdn.com/images/dev_ssj/post/c8792e14-96ff-45aa-b8ee-958c7b53465f/image.png" />

<blockquote>
<p>📌요구사항</p>
</blockquote>
<ul>
<li>회원과 팀 엔티티가 있다.</li>
<li>회원은 하나의 팀에만 소속될 수 있고, 하나의 팀에는 여러명의 회원이 소속 될 수 있다.</li>
<li>회원과 팀은 다대일 관계다.</li>
</ul>
<h3 id="2-객체의-참조와-테이블의-외래-키를-매핑">2. 객체의 참조와 테이블의 외래 키를 매핑</h3>
<h4 id="member-엔티티">Member 엔티티</h4>
<pre><code class="language-java">@Entity
public class Member{

    @Id @GeneratedValue
    private Long id;

    @Column(name=&quot;USERNAME&quot;)
    private String name;
    private int age;

    @ManyToOne
    @JoinColum(name=&quot;TEAM_ID&quot;)
    private Team team;
}</code></pre>
<h4 id="team-엔티티">Team 엔티티</h4>
<pre><code class="language-java">@Entity
public class Team{

    @Id @GeneratedValue
    @Column(name=&quot;TEAM_ID&quot;)
    private Long id;

    private String name;
}</code></pre>
<blockquote>
<ul>
<li><code>@ManyToOne</code> : <code>다대일</code> 관계임을 알려줌.</li>
</ul>
</blockquote>
<ul>
<li>하나의 팀에 여러멤버가 소속되므로 멤버가 N, 팀이 1이다.<ul>
<li><code>@JoinColum</code> : <code>조인해야하는 컬럼</code>을 알려줌.</li>
<li>TEAM_ID 컬럼과 조인해야하므로 name=&quot;TEAM_ID&quot; 작성</li>
</ul>
</li>
</ul>
<img src="https://velog.velcdn.com/images/dev_ssj/post/a4905401-8ebf-43da-891d-9cec9fa1a791/image.png" />

<h3 id="3-연관관계-저장-및-조회">3. 연관관계 저장 및 조회</h3>
<pre><code class="language-java">//팀 저장
Team team = new Team();
team.setName(&quot;TeamA&quot;);
em.persist(team);

//회원 저장
Member member = new Member();
member.setName(&quot;member1&quot;);
member.setTeam(team);    //단방향 연관관계 설정. 참조 저장
em.persist(member);

//조회
Member findMember = em.find(Member.class, member.getId());

//참조를 사용해서 연관관계 조회
Team findTeam = findMember.getTeam();</code></pre>
<h3 id="4-연관관계-수정">4. 연관관계 수정</h3>
<pre><code class="language-java">//팀 저장
Team teamB = new Team();
teamB.setName(&quot;TeamB&quot;);
em.persist(teamB);

//회원1에 새로운 팀B 설정
member.setTeam(teamB);</code></pre>
<h1 id="💡양방향-연관관계">💡양방향 연관관계</h1>
<h2 id="📗양방향-연관관계-설계">📗양방향 연관관계 설계</h2>
<h3 id="1-양방향-객체-연관관계-사용">1. 양방향 객체 연관관계 사용</h3>
<img src="https://velog.velcdn.com/images/dev_ssj/post/34707e97-7ff0-4f81-9519-a39d07bd3424/image.png" />

<blockquote>
<ul>
<li>테이블의 연관관계에는 방향이란게 없다. </li>
</ul>
</blockquote>
<ul>
<li>FK 하나로 서로의 연관을 모두 알수 있기 때문.<ul>
<li>하지만 객체 연관관계에서는 Member에서 Team으로 갈 수 있지만, Team에서 Member로 갈수 있는 방법이 없다.
➡️Team에서 Member로 갈 수 있도록 Team에 리스트를 넣어줘야한다.</li>
</ul>
</li>
</ul>
<h3 id="2-객체의-양방향-매핑">2. 객체의 양방향 매핑</h3>
<h4 id="member-엔티티-1">Member 엔티티</h4>
<pre><code class="language-java">@Entity
public class Member{

    @Id @GeneratedValue
    private Long id;

    @Column(name=&quot;USERNAME&quot;)
    private String name;
    private int age;

    @ManyToOne
    @JoinColum(name=&quot;TEAM_ID&quot;)
    private Team team;
}</code></pre>
<blockquote>
<ul>
<li>Member 엔티티는 단방향 매핑때와 동일. 달라진게 없다.</li>
</ul>
</blockquote>
<h4 id="team-엔티티-1">Team 엔티티</h4>
<pre><code class="language-java">@Entity
public class Team{

    @Id @GeneratedValue
    @Column(name=&quot;TEAM_ID&quot;)
    private Long id;

    private String name;

    @OneToMany(mappedBy = &quot;team&quot;)
    List&lt;Member&gt; members = new ArrayList&lt;Member&gt;();
}</code></pre>
<blockquote>
<ul>
<li><code>@OneToMany</code> : <code>일대다</code> 관계임을 알려줌.</li>
</ul>
</blockquote>
<ul>
<li>Member에서 Team으로 가는게 다대일이면 Team에서 Member로 가는건 일대다<ul>
<li><code>mappedBy</code> : 무엇과 조인이 걸려있는지(매핑되었는지 알려줌.)</li>
</ul>
</li>
<li>Member의 <code>JoinColumn</code>되어 있는 <code>객체</code>를 적어주면 된다.</li>
</ul>
<h4 id="양방향-조회">양방향 조회</h4>
<pre><code class="language-java">//team 조회
Team findTeam = em.find(Team.class, team.getId());

//역방향 조회
int memberSize = findTeam.getMembers().size();</code></pre>
<h1 id="💡연관관계의-주인과-mappedby">💡연관관계의 주인과 mappedBy</h1>
<blockquote>
<p>객체와 테이블간에 연관관계 맺는 차이를 이해해야 mappedBy를 이해하기 쉽다.</p>
</blockquote>
<h2 id="📗객체와-테이블이-관계를-맺는-차이">📗객체와 테이블이 관계를 맺는 차이</h2>
<img src="https://velog.velcdn.com/images/dev_ssj/post/0db1c453-c5cf-474e-91f5-dafa08b201b1/image.png" />

<ul>
<li>객체 연관관계 = 2개<ul>
<li>회원 -&gt; 팀 연관관계 1개(List member. 단방향)</li>
<li>팀 -&gt; 회원 연관관계 1개(Team team. 단방향)</li>
<li>테이블 연관관계 = 1개</li>
<li>회원 &lt;-&gt; 팀 연관관계 1개(FK. 양방향)   </li>
</ul>
</li>
</ul>
<p>➡️<strong>객체의 양방향 관계는 서로 다른 단방향 관계 2개다!</strong> </p>
<img src="https://velog.velcdn.com/images/dev_ssj/post/8a2ea971-f127-4597-9d10-dfba185a5259/image.png" />

<blockquote>
<p>즉, 테이블은 외래키 하나로 두 테이블의 연관관계를 정리하지만 <strong>객체의 양방향 관계는 단방향관계 2개이므로 둘 중 한개의 연관관계로 외래키를 관리해야한다.</strong>
➡️여기서 결정한 한개의 연관관계가 바로 <code>연관관계의 주인</code>이 되는 것!</p>
</blockquote>
<h2 id="📗연관관계의-주인">📗연관관계의 주인</h2>
<h3 id="양방향-매핑-규칙">양방향 매핑 규칙</h3>
<ul>
<li>객체의 두 관계중 하나를 <code>연관관계의 주인</code>으로 지정</li>
<li>연관관계의 주인만이 <code>외래 키를 관리</code>(등록, 수정)</li>
<li>주인이 아닌쪽은 <code>읽기만</code> 가능</li>
<li>주인은 mappedBy 속성 <code>사용 불가</code></li>
<li>주인이 아니면 <code>mappedBy</code> 속성으로 주인 지정</li>
</ul>
<h3 id="그렇다면-누구를-주인으로">그렇다면, 누구를 주인으로?</h3>
<blockquote>
<p>➡️<strong>외래키가 있는 곳을 주인으로 정해라</strong></p>
</blockquote>
<img src="https://velog.velcdn.com/images/dev_ssj/post/41e4ccad-4ff0-4682-8ad7-668500a9fab8/image.png" />

<ul>
<li>위의 연관관계에서는 <code>Member.team</code>이 연관관계의 주인이다<ul>
<li>테이블 연관관계에서 Member테이블에 FK가 있으니까!</li>
</ul>
</li>
</ul>
<h3 id="양방향-연관관계-예시">양방향 연관관계 예시</h3>
<h4 id="member-엔티티-2">Member 엔티티</h4>
<pre><code class="language-java">@Entity
public class Member{

    @Id @GeneratedValue
    private Long id;

    @Column(name=&quot;USERNAME&quot;)
    private String name;
    private int age;

    @ManyToOne
    @JoinColum(name=&quot;TEAM_ID&quot;)
    private Team team;

    ...
    //연관관계 편의 메서드 작성
    public void changeTeam(Team team){
        this.team = team;
        team.getMember().add(this);
    }
}</code></pre>
<h4 id="team-엔티티-2">Team 엔티티</h4>
<pre><code class="language-java">@Entity
public class Team{

    @Id @GeneratedValue
    @Column(name=&quot;TEAM_ID&quot;)
    private Long id;

    private String name;

    @OneToMany(mappedBy = &quot;team&quot;)
    List&lt;Member&gt; members = new ArrayList&lt;Member&gt;();
}</code></pre>
<h4 id="실행-코드">실행 코드</h4>
<pre><code class="language-java">    //team 저장
    Team team = new team();
    team.setName(&quot;teamA&quot;);
    em.persist(team);

    //MEMBER 저장
    Member member = new Member();
    member.setName(&quot;memberA&quot;);

    //연관관계 편의 메서드를 사용해서 값 저장
    member.changeTeam(team);    
    em.persist</code></pre>
<h3 id="📌양방향-매핑-정리">📌양방향 매핑 정리</h3>
<ul>
<li>단방향 매핑만으로도 이미 연관관계 매핑은 종료</li>
<li>단방향 매핑을 잘하고 양방향은 필요할 때 추가해도 됨</li>
<li>연관관계의 주인은 외래 키의 위치를 기준으로 정해야한다</li>
</ul>