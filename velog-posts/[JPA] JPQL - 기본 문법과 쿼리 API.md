<h1 id="💡jpql">💡JPQL</h1>
<blockquote>
<ul>
<li>JPQL은 객체지향 쿼리 언어다. 
 ➡️ 테이블을 대상으로 쿼리하는 것이 아니라 <code>엔티티 객체를 대상으로 쿼리</code>한다.</li>
</ul>
</blockquote>
<ul>
<li>JPQL은 SQL을 추상화해서 특정데이터베이스 SQL에 의존하지 않는다</li>
<li>JPQL은 결국 SQL로 변환된다.</li>
</ul>
<img src="https://velog.velcdn.com/images/dev_ssj/post/47ae105f-0ebf-4b9d-aecd-4a20b2e44c38/image.png" />

<pre><code class="language-java">@Entity
public class Member {
    @Id @GeneratedValue
    private Long id;
    private String username;
    private int age;

    @ManyToOne
    @JoinColumn(name = &quot;TEAM_ID&quot;)
    private Team team;

        //getter , setter...
}
@Entity
public class Team {
    @Id @GeneratedValue
    private Long id;
    private String name;

    @OneToMany(mappedBy = &quot;team&quot;)
    private List&lt;Member&gt; members = new ArrayList&lt;Member&gt;();
    public Long getId() {
        return id;
    }
}

@Entity
@Table(name = &quot;ORDERS&quot;)
public class Order {
    @Id @GeneratedValue
    private Long id;
    private int orderAmount;
    @Embedded
    private Address address;
    @ManyToOne
    @JoinColumn(name = &quot;PRODUCT_ID&quot;)
    private Product product;

}
@Entity
public class Product {
    @Id @GeneratedValue
    private Long id;
    private String name;
    private int price;
    private int stockAmound;
}
@Embeddable
public class Address {
    private String city;
    private String street;
    private String zipcode;
}</code></pre>
<h1 id="💡jpql---기본-문법과-쿼리api">💡JPQL - 기본 문법과 쿼리API</h1>
<blockquote>
<p>JPQL의 기본 문법과 쿼리API에 대해 알아보자</p>
</blockquote>
<h2 id="📗jpql-문법">📗JPQL 문법</h2>
<img src="https://velog.velcdn.com/images/dev_ssj/post/c9ee80f8-0f9b-432d-bdd8-6dda5b4e068b/image.png" />

<blockquote>
<p> SELECT m FROM Member as m WHERE m.age &gt; 18</p>
</blockquote>
<ul>
<li>엔티티와 속성은 대소문자 구분<ul>
<li>Member, age</li>
</ul>
</li>
<li>JPQL 키워드는 대소문자 구분X<ul>
<li>SELECT, FROM, WHERE</li>
</ul>
</li>
<li><code>엔티티 명을 사용</code>한다. 테이블 명이 아니다!(Member)</li>
<li><strong>별칭은 필수(m)</strong> <ul>
<li>as는 생략 가능</li>
</ul>
</li>
</ul>
<h2 id="📗집합과-정렬">📗집합과 정렬</h2>
<ul>
<li><code>count(m)</code> : 회원 수</li>
<li><code>sum(m.age)</code> : 나이 합</li>
<li><code>avg(m.age)</code> : 평균 나이</li>
<li><code>max(m.age)</code> : 최대 나이</li>
<li><code>min(m.age)</code> : 최소 나이</li>
<li><code>group by</code>, <code>having</code></li>
</ul>
<h2 id="📗typequery와-query">📗TypeQuery와 Query</h2>
<ul>
<li>TypeQuery : <code>반환 타입이 명확</code>할 때 사용</li>
<li>Query : <code>반환 타입이 명확하지 않을 때</code> 사용<pre><code class="language-java">//TypeQuery
TypedQuery&lt;Member&gt; query = 
                  em.createQuery(&quot;SELECT m FROM Member m&quot;, Member.class);
</code></pre>
</li>
</ul>
<p>//Query
Query query = em.createQuery(&quot;SELECT m.username, m.age FROM Member m&quot;);</p>
<pre><code>## 📗결과 조회 API
&gt;- query.getResultList() : `결과가 하나 이상`일 때 리스트 반환
- query.getSingleResult() : `결과가 정확히 하나`일 때 단일 객체 
반환
   - 결과가 없으면 : `javax.persistence.NoResultException`
   - 결과가 둘 이상이면 : `javax.persistence.NonUniqueResultException`

```java
//결과가 하나 이상
TypedQuery&lt;Member&gt; query = em.createQuery(&quot;SELECT m FROM Meber m&quot;, Member.class);
List&lt;Member&gt; resultList = query.getResultList();
//결과가 하나
TypedQuery&lt;Member&gt; query = em.createQuery(&quot;SELECT m FROM Meber m WHERE m.id = 10&quot;, Member.class);
Member Result = query.getSingleResult();</code></pre><h2 id="📗파라미터-바인딩">📗파라미터 바인딩</h2>
<h3 id="1-이름-기준">1. 이름 기준</h3>
<pre><code class="language-java">//username이 member1인 회원 찾기
Member result = 
            em.createQuery(&quot;SELECT m FROM Member m WHERE m.username = :username&quot;, Member.class)
            .setParameter(&quot;username&quot;, &quot;member1&quot;)
            .getSingleResult();</code></pre>
<blockquote>
<ul>
<li>:username : 파라미터로 바인딩할 변수명 </li>
</ul>
</blockquote>
<ul>
<li>.setParameter(&quot;파라미터로 바인딩할 변수명&quot;, &quot;바인딩할 문자&quot;)</li>
</ul>
<h3 id="2-위치-기준">2. 위치 기준</h3>
<pre><code class="language-java">//username이 member1인 회원 찾기
Member result = 
            em.createQuery(&quot;SELECT m FROM Member m WHERE m.username = ?1&quot;, Member.class)
            .setParameter(1, &quot;member1&quot;)
            .getSingleResult();</code></pre>
<blockquote>
<p>❌<span style="color: red;"><strong>가급적 위치기준은 사용하지 말기</strong></span>
 중간에 변수를 끼워넣게 되면 다 순서가 밀림!</p>
</blockquote>