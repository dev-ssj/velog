<h1 id="💡jpql-소개">💡JPQL 소개</h1>
<h2 id="📗jpa는-다양한-쿼리-방법을-지원">📗JPA는 다양한 쿼리 방법을 지원</h2>
<ul>
<li><strong>JPQL</strong></li>
<li>JPA Criteria</li>
<li><strong>QueryDSL</strong></li>
<li>네이티브 SQL</li>
<li>JDBC API 직접 사용, MyBatis, SpringJdbcTemplate 함께 사용</li>
</ul>
<h2 id="📗jpql-이란">📗JPQL 이란?</h2>
<ul>
<li>가장 단순한 조회 방법<ul>
<li>EntityManager.find()</li>
<li>객체 그래프 탐색(a.getB().getC())</li>
</ul>
</li>
</ul>
<blockquote>
<p>📌나이가 18살 이상인 회원을 모두 검색하고 싶다면?</p>
</blockquote>
<h2 id="📗jpql">📗JPQL</h2>
<ul>
<li>JPA를 사용하면 <code>엔티티 객체를 중심으로 개발</code>한다.</li>
<li>문제는 검색쿼리</li>
<li>JPA는 검색을 할 때도 테이블이 아닌 <code>엔티티 객체를 대상으로 검색</code>한다.</li>
<li>모든 DB 데이터를 객체로 변환해서 검색하는 것은 불가능</li>
<li>애플리케이션이 필요한 데이터만 DB에서 불러오려면 결국 <code>검색 조건이 포함된 SQL</code>이 필요</li>
<li>JPA는 SQL을 추상화한 <code>JPQL</code>이라는 객체 지향 쿼리 언어 제공</li>
<li>SQL 문법과 유사하며 <code>SELECT</code>, <code>FROM</code>, <code>WHERE</code>, <code>GROUP BY</code>, <code>HAVING</code>, <code>JOIN</code>지원</li>
<li>JPQL은 <code>엔티티 객체</code>를 대상으로 쿼리</li>
<li>SQL은 <code>데이터 베이스 테이블</code>을 대상으로 쿼리</li>
</ul>
<pre><code class="language-java">String jpql = &quot;select m from Member m where m.age&gt;18&quot;;
List&lt;Member&gt; result = em.createQuery(jpql,Member.class)
                        .getResultList();
//실행된 SQL
select m.id as id, m.age as age, m.username as username, m.team_id as team_id
from Member m
where m.age &gt; 18</code></pre>
<h2 id="📗criteria">📗Criteria</h2>
<pre><code class="language-java">//Criteria 사용 준비
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery&lt;Member&gt; query = cb.createQuery(Member.class);

//루트 클래스 (조회를 시작할 클래스)
Root&lt;Member&gt; m = query.from(Member.class);

//쿼리 생성 
CriteriaQuery&lt;Member&gt; cq =  query.select(m).where(cb.equal(m.get(&quot;username&quot;), “kim”));
List&lt;Member&gt; resultList = em.createQuery(cq).getResultList()</code></pre>
<ul>
<li>문자가 아닌 자바코드로 JPQL을 작성할 수 있다.</li>
<li>JPQL 빌더 역할</li>
<li>JPA 공식 기능</li>
</ul>
<blockquote>
<p>❌<span style="color: red;"><strong>실무에선 거의 사용되지 않는다.</strong></span>
너무 복잡하고 실용성이 없음
➡️Criteria 대신에 <strong>QueryDSL 사용 권장</strong></p>
</blockquote>
<h2 id="📗querydsl">📗QueryDSL</h2>
<pre><code class="language-java">//JPQL
//select m from Member m where m.age &gt; 18
JPAFactoryQuery queryFactory= new JPAQueryFactory(em); 
QMember m = QMember.member;
List&lt;Member&gt; list = queryFactory.selectFrom(m) 
                                .where(m.age.gt(18))
                                .orderBy(m.name.desc())
                                .fetch();</code></pre>
<ul>
<li>문자가 아닌 자바코드로 JPQL 작성 가능</li>
<li>JPQL 빌더 역할</li>
<li>컴파일 시점에 문법 오류를 찾을 수 있다</li>
<li>동적쿼리 작성에 편리함</li>
<li>단순하고 쉽다</li>
<li>** 실무 사용 권장 **</li>
</ul>
<h2 id="📗네이티브-sql">📗네이티브 SQL</h2>
<pre><code class="language-java">String sql =&quot;SELECT ID, AGE, TEAM_ID, NAME FROM MEMBER WHERE NAME = ‘kim’&quot;;
List&lt;Member&gt; resultList = em.createNativeQuery(sql, Member.class)
                            .getResultList();</code></pre>
<ul>
<li>JPA가 제공하는 SQL을 직접 사용하는 기능</li>
<li>JPQL로 해결할 수 없는 특정 데이터베이스에 의존적인 기능</li>
<li>예) 오라클 CONNECT BY, 특정 DB만 사용하는 SQL 힌트</li>
</ul>
<h2 id="📗jdbc-직접-사용-springjdbctemplate-등">📗JDBC 직접 사용, SpringJdbcTemplate 등</h2>
<ul>
<li>JPA를 사용하면서 JDBC 커넥션을 직접 사용하거나, 스프링 SpringJdbcTemplate, 마이바티스 등을 함께 사용 가능</li>
<li>단, 영속성 컨텍스트를 적절한 시점에 강제로 플러시 필요<ul>
<li>JPA를 우회해서 SQL을 실행하기 직전에 영속성 컨텍스트 수동 플러스</li>
</ul>
</li>
</ul>