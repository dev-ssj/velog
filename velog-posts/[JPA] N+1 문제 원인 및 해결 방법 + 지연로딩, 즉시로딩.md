<h1 id="💡n1-문제란">💡N+1 문제란?</h1>
<blockquote>
<p>N+1문제란 ORM 연관 관계에서 발생하는 이슈로, 연관 관계가 설정된 엔티티를 조회할 경우에 조회된 데이터 갯수(n) 만큼 연관관계의 조회 쿼리가 추가로 발생하여 데이터를 읽어오게 된다. 즉, <strong>1번의 쿼리를 날렸을 때 의도하지 않은 N번의 쿼리가 추가적으로 실행되는 것</strong>이다. 이를 N+1 문제라고 한다.
예를 들어, 데이터 조회 시 1번만 조회할 것을 100만개 종류의 데이터가 있어 1,000,001번 DB조회하게 되어 많은 량의 쿼리가 발생하고 성능저하를 일으킨다.</p>
</blockquote>
<hr />
<h2 id="✅언제-발생하는가">✅언제 발생하는가?</h2>
<blockquote>
<p>N+1문제는 JPA Repository를 활용해 1:N 또는 N:1 관계를 가진 엔티티를 조회할 때 발생하며, JPA Fetch EAGER전략으로 데이터를 조회하는 경우나, LAZY 전략으로 데이터를 가져온 이후에 연관관계인 하위 엔티티를 다시 조회하는 경우 발생한다.</p>
</blockquote>
<h3 id="📌eager즉시로딩인-경우">📌EAGER(즉시로딩)인 경우</h3>
<ol>
<li>JPQL에서 만든 SQL을 통해 데이터를 조회</li>
<li>이후 JPA에서 Fetch 전략을 가지고 해당 데이터의 연관 관계인 하위 엔티티들을 추가 조회</li>
<li>2번 과정으로 N+1 문제 발생</li>
</ol>
<h3 id="📌lazy지연로딩인-경우">📌LAZY(지연로딩)인 경우</h3>
<ol>
<li>JPQL에서 만든 SQL을 통해 데이터를 조회</li>
<li>JPA에서 Fetch 전략을 가지지만, 지연 로딩이기 때문에 추가 조회는 하지 않음</li>
<li>하지만, 하위 엔티티를 가지고 작업하게 되면 추가 조회가 발생하기 때문에 결국 N+1 문제 발생</li>
</ol>
<hr />
<h2 id="1-fetch-type이란">1. Fetch Type이란</h2>
<p>FetchType이란, <strong>JPA가 하나의 Entity를 조회할 때, 연관관계에 있는 객체들을 어떻게 가져올 것이냐를 나타내는 설정값</strong>이다.
JPA는 사용자가 직접 쿼리를 생성하지 않고, JPQL을 이용하여 쿼리문을 생성하기 때문에 객체와 필드를 보고 쿼리를 생성한다.
따라서, 다른 객체와 연관관계 매핑이 되어있으면 그 객체들까지 조회하게 되는데, 이때 이 객체를 어떻게 불러올 것인가를 설정할 수 있다.
fetch의 디폴트 값은 <strong>@xxToOne에서는 EAGER, @xxToMany에서는 LAZY</strong>이다.</p>
<hr />
<h2 id="2-즉시로딩fetchtypeeager">2. 즉시로딩(FetchType.EAGER)</h2>
<h3 id="🔍즉시로딩의-개념">🔍즉시로딩의 개념</h3>
<p>즉시로딩이란 <strong>데이터를 조회할 때 연관된 모든 객체의 데이터까지 바로 한번에 불러오는 것</strong>이다.
부모 엔티티를 조회하는 순간 동작하며, <code>@ManyToOne</code>, <code>@OneToOne</code>의 기본값이다.</p>
<h3 id="📍예시">📍예시</h3>
<pre><code class="language-java">@Entity
public class Member {

    @Id @GeneratedValue
    private Long id;
    private String username;

    @ManyToOne(fetch = FetchType.EAGER) //Team을 조회할 때 즉시로딩사용
    @JoinColumn(name = &quot;team_id&quot;)
    Team team;
}

@Entity
public class Team {

    @Id @GeneratedValue
    private Long id;
    private String teamname;
}</code></pre>
<ul>
<li><p>JPQL로 Member 1건 조회</p>
<pre><code class="language-java">Member findMember = em.createQuery(&quot;select m from Member m&quot;, Member.class).getSingleResult();</code></pre>
</li>
<li><p>실제 SQL 코드</p>
<pre><code class="language-java">//멤버를 조회하는 쿼리
select
  member0_.id as id1_0_,
  member0_.team_id as team_id3_0_,
  member0_.username as username2_0_ 
from
  Member member0_
</code></pre>
</li>
</ul>
<p>//팀을 조회하는 쿼리
select
    team0_.id as id1_3_0_,
    team0_.name as name2_3_0_ 
from
    Team team0_ 
where
    team0_.id=?</p>
<pre><code>&gt;➡️즉시로딩 사용시, Member를 조회하는 시점에 바로 연관된 Team까지 불러오는 쿼리를 날린다.
➡️만약 Member를 조회하는 JPQL을 날렸는데 연관된 Team이 1000개라면 N+1 문제가 발생할 수도 있으며, 불필요한 데이터 조회로 인해 성능 악화 가능성이 있다.

---

## 3. 지연로딩(FetchType.LAZY)
### 🔍지연로딩의 개념
지연로딩이란 **필요한 시점에 연관된 객체의 데이터를 불러오는 것**이다.
Getter 호출 등 실제 데이터 접근 시 동작하며, `@OneToMany`, `@ManyToMany`의 기본값이다.

### 📍예시
```java
@Entity
public class Member {

    @Id @GeneratedValue
    private Long id;
    private String username;

    @ManyToOne(fetch = FetchType.LAZY) //Team을 조회할 때 지연로딩 사용
    @JoinColumn(name = &quot;team_id&quot;)
    Team team;
}

@Entity
public class Team {

    @Id @GeneratedValue
    private Long id;
    private String teamname;
}</code></pre><ul>
<li><p>JPQL로 Member 1건 조회</p>
<pre><code class="language-java">Member findMember = em.createQuery(&quot;select m from Member m&quot;, Member.class).getSingleResult();</code></pre>
</li>
<li><p>실제 SQL 코드</p>
<pre><code class="language-java">//Team을 조회하는 쿼리가 나가지 않음.
select
  member0_.id as id1_0_,
  member0_.team_id as team_id3_0_,
  member0_.username as username2_0_ 
from
  Member member0_</code></pre>
<blockquote>
<p>➡️지연로딩 사용시, Member를 조회하는 시점이 아닌 실제 Team을 사용하는 시점에 쿼리가 나간다.
➡️성능 최적화에 유리하지만, 반복문에서 호출 시 N+1 발생 위험이 있다.</p>
</blockquote>
</li>
</ul>
<hr />
<h2 id="4-지연로딩-vs-즉시로딩">4. 지연로딩 VS 즉시로딩</h2>
<table>
<thead>
<tr>
<th>항목</th>
<th>지연로딩 (LAZY)</th>
<th>즉시로딩 (EAGER)</th>
</tr>
</thead>
<tbody><tr>
<td>의미</td>
<td>실제 데이터를 <strong>사용하는 시점</strong>에 쿼리 실행</td>
<td>연관 엔티티를 <strong>즉시 함께 조회</strong></td>
</tr>
<tr>
<td>동작 시점</td>
<td>Getter 호출 등 <strong>실제 접근 시</strong></td>
<td>부모 엔티티를 <strong>조회하는 순간</strong></td>
</tr>
<tr>
<td>사용 기본</td>
<td><code>@OneToMany</code>, <code>@ManyToMany</code></td>
<td><code>@ManyToOne</code>, <code>@OneToOne</code></td>
</tr>
<tr>
<td>성능 영향</td>
<td>초기에는 가볍지만 N+1 발생 위험</td>
<td>초기 쿼리 무거워질 수 있음</td>
</tr>
<tr>
<td>N+1 발생 가능</td>
<td>✅ *<em>발생 가능 (많이 발생함) *</em></td>
<td>✅ 발생 가능</td>
</tr>
</tbody></table>
<blockquote>
<p>가급적 실무에서는 지연로딩만 사용하는 것이 좋으며, 즉시로딩 사용시 다음과 같은 문제점이 발생할 수 있다.</p>
</blockquote>
<ul>
<li>N+1 쿼리 문제</li>
<li>메모리 부하(시로딩을 사용하면 연관된 모든 자식 객체들도 함께 메모리에 로딩되므로)</li>
<li>데이터 무결성</li>
<li>데이터베이스 성능</li>
</ul>
<hr />
<h2 id="✅n1-문제-해결-방법">✅N+1 문제 해결 방법</h2>
<h3 id="1-fetch-join-사용">1. Fetch join 사용</h3>
<h4 id="🔍원리">🔍원리</h4>
<p>N+1 자체가 발생하는 이유는 한쪽 테이블만 조회하고 연결된 다른 테이블은 따로 조회하기 때문이다. 즉, <strong>미리 두 테이블을 JOIN하여 한번에 모든 데이터를 가져올 수 있다면 N+1 문제는 발생하지 않는다.</strong>
➡️JPA의 <code>JOIN FETCH</code>를 이용해서 <strong>연관 엔티티를 즉시 조인하여 한번에 조회</strong>할 수 있다.
➡️<strong>지연로딩이라도 Fetch Join을 사용하면 즉시 로딩처럼 동작</strong>한다.</p>
<h4 id="📍예시-1">📍예시</h4>
<pre><code class="language-java">//JPQL 작성
@Query(&quot;SELECT p FROM Post p JOIN FETCH p.author&quot;)
List&lt;Post&gt; findAllWithAuthor();</code></pre>
<ul>
<li>쿼리 결과<pre><code class="language-sql">SELECT p.*, u.* FROM post p
JOIN user u ON p.author_id = u.id;</code></pre>
</li>
</ul>
<h4 id="⭕장점">⭕장점</h4>
<ul>
<li>쿼리 1번으로 연관 엔티티까지 모두 조회가능</li>
<li>가장 간단하고 직관적인 N+1 해결책</li>
</ul>
<h4 id="❗단점">❗단점</h4>
<ul>
<li>페이징 불가</li>
<li>둘 이상의 컬렉션 페치 조인 불가</li>
<li>페치 조인 대상에는 별칭을 줄 수 없음</li>
</ul>
<hr />
<h3 id="2-entitygraph-사용">2. @EntityGraph 사용</h3>
<p><strong>Spring Data JPA가 제공하는 기능</strong>으로, <code>@EntityGraph</code> 의 attributePaths에 쿼리 수행 시, 바로 가져올 필드명을 지정하면 <strong>LAZY가 아닌 EAGER 조회</strong>로 가져오게 된다.
Fetch join과 동일하게 JPQL을 사용하여 query 문을 작성하고 필요한 연관관계를 EntityGraph에 설정하면 된다. Fetch join과는 다르게 join 문이 outer join으로 실행된다.</p>
<h4 id="📍예시-2">📍예시</h4>
<pre><code class="language-java">@EntityGraph(attributePaths = {&quot;author&quot;})
List&lt;Post&gt; findAll();

//또는 메서드 위에 직접 지정
@EntityGraph(attributePaths = {&quot;author&quot;})
@Query(&quot;SELECT p FROM Post p&quot;)
List&lt;Post&gt; findAllWithAuthorGraph();
</code></pre>
<h4 id="⭕장점-1">⭕장점</h4>
<ul>
<li>코드가 간결하고 재사용성 높음</li>
<li>페이징 가능(단, <code>@ManyToOne</code>과 같이 단일 관계만 로딩할 때)</li>
</ul>
<h4 id="❗단점-1">❗단점</h4>
<ul>
<li>복잡한 연관관계가 많거나 깊어지면 가독성 떨어짐</li>
<li>여러 컬렉션 fetch시 불안정</li>
</ul>
<hr />
<h3 id="3-batchsize-지정">3. BatchSize() 지정</h3>
<h4 id="🔍원리-1">🔍원리</h4>
<p>Hibernate에서 연관 엔티티를 <strong>일정 수(batch)로 묶어서 한 번에 조회하는 전략</strong>이다.
→ LAZY 상태에서도 N+1 대신 <code>IN 쿼리</code>로 여러 건 한꺼번에 로딩</p>
<h4 id="📍예시-3">📍예시</h4>
<ul>
<li>설정 방법(yml)<pre><code class="language-yml">spring.jpa.properties.hibernate.default_batch_fetch_size=100</code></pre>
</li>
<li>또는 엔티티에 직접 지정<pre><code class="language-java">@OneToMany(mappedBy = &quot;post&quot;)
@BatchSize(size = 100)
private List&lt;Comment&gt; comments;</code></pre>
</li>
</ul>
<h4 id="⭕장점-2">⭕장점</h4>
<ul>
<li>지연로딩을 유지하면서도 N+1 최소화 가능</li>
<li>페이징 가능한 구조 유지 가능</li>
</ul>
<h4 id="❗단점-2">❗단점</h4>
<ul>
<li>@BatchSize의 크기는 시스템의 요구 사항과 성능을 고려하여 조정해야함.
→배치 사이즈를 너무 크게 설정하면 메모리 사용량이 늘어나고 너무 작은 값을 설정하면 여전히 많은 수의 쿼리가 실행될 수 있다.</li>
</ul>
<hr />
<h3 id="4--dto-객체로-조회">4.  DTO 객체로 조회</h3>
<h4 id="🔍원리-2">🔍원리</h4>
<p>엔티티를 직접 조회하지 않고, 필요한 데이터만 뽑아서 <strong>DTO로 바로 조회</strong>하는 방식이다.
DTO로 조회하게 되면 페이징 처리까지 추가적으로 진행할 수 있으며, 불필요한 연관 로딩을 방지할 수 있다.</p>
<h4 id="📍예시-4">📍예시</h4>
<pre><code class="language-java">@Query(&quot;SELECT new com.example.dto.PostDto(p.title, u.name) FROM Post p JOIN p.author u&quot;)
List&lt;PostDto&gt; findPostDtos();</code></pre>
<ul>
<li><p>DTO클래스</p>
<pre><code class="language-java">public class PostDto {
  private String title;
  private String authorName;

  public PostDto(String title, String authorName) {
      this.title = title;
      this.authorName = authorName;
  }
}</code></pre>
<h4 id="⭕장점-3">⭕장점</h4>
</li>
<li><p>필요한 필드만 조회하므로 성능 최적화</p>
</li>
<li><p>JSON 변환시 무한 루프 방지</p>
</li>
</ul>
<h4 id="❗단점-3">❗단점</h4>
<ul>
<li>DTO는 실제 엔티티가 아니므로, 영속성 컨텍스트에 관리되지 않는다 -&gt; 오로지 조회할 때만 가능</li>
</ul>