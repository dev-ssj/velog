<h1 id="💡다형성-쿼리">💡다형성 쿼리</h1>
<img src="https://velog.velcdn.com/images/dev_ssj/post/832dd487-e924-434a-8ea5-1f1508cdee70/image.png" />

<ul>
<li>Item : 부모객체</li>
<li>Album, Movie, Book : 자식 객체</li>
</ul>
<h2 id="📗type">📗TYPE</h2>
<ul>
<li><p>조회 대상을 <code>특정 자식으로 한정</code></p>
</li>
<li><p>예) Item 중 Book, Movie를 조회하라</p>
<pre><code class="language-java">  //JPQL
  select i from Item i where type(i) IN(Book, Movie)

  //SQL
  select i from Item i where i.DTYPE in('B', 'M');</code></pre>
</li>
</ul>
<h3 id="📗treatjpa-21">📗TREAT(JPA 2.1)</h3>
<ul>
<li><p>자바의 <code>타입 캐스팅과 유사</code>(형변환)</p>
</li>
<li><p>상속 구조에서 부모 타입을 특정 자신 타입으로 다룰 때 사용</p>
</li>
<li><p>FROM, WHERE, SELECT(하이버네이트 지원) 사용</p>
</li>
<li><p>예) 부모인 Item과 자식 Book이 있다.</p>
<pre><code class="language-java">  //JPQL
  select from Item i where treat(i as Book).author = 'kim';

  //SQL
  select i.* from Item i where i.DTYPE = 'B' and i.author = 'kim';</code></pre>
</li>
</ul>
<h1 id="💡엔티티-직접-사용">💡엔티티 직접 사용</h1>
<h2 id="기본-키-값">기본 키 값</h2>
<ul>
<li><p>JPQL에서 엔티티를 직접 사용하면 SQL에서 <code>해당 엔티티의 기본 키</code> 값을 사용</p>
<pre><code class="language-java"> //JPQL
 select count(m.id) from Member m //엔티티의 아이디를 사용
 select count(m) from Member m //엔티티를 직접 사용

 //SQL(JPQL 둘 다 같은 다음 SQL 실행)
 select count(m.id) as cnt from Member m</code></pre>
</li>
</ul>
<ul>
<li><p>파라미터로 엔티티를 넘겨주거나 식별자를 직접 전달하더라도 실행되는 SQL은 같다.</p>
<pre><code class="language-java">  //엔티티를 파라미터로 전달
  String jpql = &quot;select m from Member m where m = :member&quot;;
  List resultList = em.createQuery(jpql)
                      .setParameter(&quot;member&quot;, member)
                      .getResultList();

  //식별자를 직접 전달
  String jpql = &quot;select m from Member m where m.id =     :memberId&quot;;
  List resultList = em.createQuery(jpql)
                      .setParameter(&quot;memberId&quot;, memberId)
                      .getResultList();</code></pre>
</li>
<li><p>실행된 SQL은 동일하다.</p>
<pre><code class="language-sql">  select m.* from Member m where m.id = ?</code></pre>
</li>
</ul>
<h2 id="📗외래-키-값">📗외래 키 값</h2>
<ul>
<li><p>기본 키와 로직은 동일하다.</p>
<pre><code class="language-java">  Team team = em.find(Team.class, 1L);

  //엔티티를 파라미터로 전달
  String query = &quot;select m from Member m where m.team = :team&quot;;
  List resultList = em.createQuery(query)
                      .setParameter(&quot;team&quot;, team)
                      .getResultList();
  //식별자를 직접 전달
  String query = &quot;select m from Member m where m.team.id = :teamId&quot;;
  List resultList = em.createQuery(query)
                      .setParameter(&quot;teamId&quot;, teamId)
                      .getResultList();</code></pre>
</li>
<li><p>실행된 SQL</p>
<pre><code class="language-sql">  select m.* from Member m where m.team_id = ?</code></pre>
</li>
</ul>
<h1 id="💡named-쿼리">💡Named 쿼리</h1>
<ul>
<li>미리 정의해서 이름을 부여해두고 사용하는 JPQL</li>
<li>정적 쿼리</li>
<li>어노테이션, XML에 정의</li>
<li>애플리케이션 로딩 시점에 초기화 후 재사용</li>
<li><strong>애플리케이션 로딩 시점에 쿼리를 검증</strong></li>
</ul>
<h3 id="📌어노테이션에-정의">📌어노테이션에 정의</h3>
<pre><code class="language-java">@Entity
@NamedQuery(
        name=&quot;Member.findByUsername&quot;,
        query=&quot;select m from Member m where m.username = :username&quot;)
public class Member {
        ...
}
...

List&lt;Member&gt; resultList = 
                    em.createNamedQuery(&quot;Member.findByUsername&quot;, Member.class)
                    .setParameter(&quot;username&quot;, &quot;회원1&quot;)
                    .getResultList();</code></pre>
<h3 id="📌xml에-정의">📌XML에 정의</h3>
<pre><code class="language-xml">//[META_INF?persistence.xml]
&lt;persistence-unit name=&quot;jpabook&quot;&gt;
        &lt;mapping-file&gt;META-INF/ormMember.xml&lt;/mapping-file&gt;

//[META-INF/ormMember.xml]
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;
&lt;entity-mappings xmlns=&quot;htt://xmlns.jcp.org/xml/ns/persistence/orm&quot; version=&quot;2.1&quot;&gt;
        &lt;named-query name=&quot;Member.findByUsername&quot;&gt;
                &lt;query&gt;
                        &lt;![CDATA[ select m from Member m where m.username = :username]]
                &lt;/query&gt;
        &lt;/named-query&gt;
&lt;/entity-mappings&gt;</code></pre>
<blockquote>
<ul>
<li>어노테이션과 XML에 정의된 Named 쿼리는 XML이 항상 우선권을 가진다.
➡️애플리케이션 운영환경에 따라 다른 XML을 배포할 수 있다.</li>
</ul>
</blockquote>
<h1 id="💡벌크-연산">💡벌크 연산</h1>
<ul>
<li>SQL의 update/delete 문을 생각하자<ul>
<li>재고가 10개 미만인 모든 상품의 가격을 10% 상승하려면?</li>
</ul>
</li>
<li>JPA의 변경 감지 기능으로 실행하려면 너무 많은 SQL이 실행되어야 한다.<ol>
<li>재고가 10개 미만인 상품을 리스트로 조회</li>
<li>상품 엔티티의 가격을 10% 증가</li>
<li>트랜잭션 커밋 시점에 변경 감지 동작</li>
</ol>
<ul>
<li><strong>변경된 데이터가 100건이라면 100번의 update SQL 실행</strong></li>
</ul>
</li>
</ul>
<h2 id="📗벌크연산-예제">📗벌크연산 예제</h2>
<ul>
<li>쿼리 한번으로 여러 테이블 로우 변경(엔티티)</li>
<li>exeuteUpdate()의 결과는 영향받은 엔티티 수 반환</li>
<li>UPDATE, DELETE 지원</li>
<li>INSERT(insert into .. select, 하이버네이트 지원)<pre><code class="language-java">String query = &quot;update Product p &quot;+
              &quot;set p.price = p.price * 1.1 where p.stockAmount &lt; :stockAmount&quot;;
</code></pre>
</li>
</ul>
<p>int resultCount = em.createQuery(qlString)
                    .setParameter(&quot;stockAmount&quot;, 10)
                .executeUpdate();</p>
<pre><code>
## 📗벌크 연산 주의점
- 벌크 연산은 영속성 컨텍스트를 무시하고 데이터베이스에 직접 쿼리
  1. 벌크 연산을 먼저 실행
  2. **벌크 연산 수행 후 영속성 컨텍스트 초기화**

```java
    Member member1 = new Member();
    member1.setUsername(&quot;회원1&quot;);
    member1.setAge(0);
    em.persist(member1);

    Member member2 = new Member();
    member2.setUsername(&quot;회원2&quot;);
    member2.setAge(0);
    em.persist(member2);

    Member member2 = new Member();
    member1.setUsername(&quot;회원2&quot;);
    member2.setAge(0);
    em.persist(member2);

    int resultCount = em.createQuery(&quot;update Member m = set m.userage=20&quot;)
                        .executeUpdate();

    System.out.println(&quot;member1 = &quot; + member1.getAge());    //0
    System.out.println(&quot;member2 = &quot; + member1.getAge());    //0
    System.out.println(&quot;member3 = &quot; + member1.getAge());    //0</code></pre><ul>
<li><p>벌크 연산이 flush를 하는 시점에는 나이가 다 0임</p>
</li>
<li><p>벌크연산 직전에 자동 fulsh -&gt; DB에 0으로 저장됨</p>
</li>
<li><p>그 후 벌크연산(UPDATE)가 나가서 20살로 업데이트됨
➡️영속성 컨텍스트에 저장되지 않고 데이터베이스에 바로 쿼리가 나간다</p>
</li>
<li><p>영속성 컨텍스트에 저장되지 않으므로 get.Age()로 멤버의 나이를 출력하면 0으로 출력된다.</p>
</li>
<li><p><em>즉, 벌크 연산 수행 후 영속성 컨텍스트 초기화를 해주는 것이 좋다. *</em></p>
<pre><code class="language-java">   int resultCount = em.createQuery(&quot;update Member m = set m.userage=20&quot;)
                       .executeUpdate();

   em.clear();    //영속성 컨텍스트 초기화
   Member findMember - em.find(Member.class, member1.getId());

   System.out.println(&quot;member1 = &quot; + member1.getAge());    //20
</code></pre>
</li>
</ul>
<p>```</p>