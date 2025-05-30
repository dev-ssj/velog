<h1 id="💡프로젝션">💡프로젝션</h1>
<ul>
<li><code>SELECT 절에 조회할 대상을 지정</code>하는 것</li>
<li>조회한 대상은 JPA가 영속성 컨텍스트로 관리한다</li>
<li>프로젝션 대상 : 엔티티, 임베디드 타입, 스칼라 타입(숫자, 문자 등 기본 데이터 타입)<pre><code class="language-sql">SELECT m FROM Member m // 엔티티 프로젝션
SELECT m.team FROM Member m // 엔티티 프로젝션
SELECT m.address FROM Member m // 임베디드 타입 프로젝션
SELECT m.username, m.age FROM Member m // 스칼라 타입 프로젝션</code></pre>
</li>
</ul>
<h2 id="📗여러-값-조회">📗여러 값 조회</h2>
<pre><code class="language-java">//meber 저장
Member member = new Member();
member.setName(&quot;member1&quot;);
member.setAge(10);
em.persist(member);

// 1.Query 타입으로 조회
List resultList1 = em.createQuery(&quot;select m.name, m.age from Member m&quot;)
                    .getResultList();

Object o = resultList1.get(0);
Object[] result1 = (Object[])o;
System.out.println(&quot;username = &quot; + result1[0]); 
System.out.println(&quot;age = &quot; + result1[1]); 

// 2.Object[] 타입으로 조회
List&lt;Object[]&gt; resultList2 = em.createQuery(&quot;select m.name, m.age from Member m&quot;)
                              .getResultList();

Object[] result2 = (Object[])o;
System.out.println(&quot;username = &quot; + result2[0]); // username = member
System.out.println(&quot;age = &quot; + result2[1]); // age = 29

// 3.new 명령어로 DTO로 조회
List&lt;MemberDTO&gt; resultList3 = em.createQuery(
        &quot;select new jpabook.jpashop.dto.MemberDTO(m.name, m.age) from Member m&quot;,
        MemberDTO.class)
    .getResultList();
</code></pre>
<blockquote>
</blockquote>
<ul>
<li><ol>
<li><code>Query 타입</code>으로 조회</li>
</ol>
</li>
<li><ol start="2">
<li><code>Object[] 타입</code>으로 조회</li>
</ol>
</li>
<li><ol start="3">
<li><code>new 명령어</code>로 조회</li>
</ol>
<ul>
<li>단순 값을 DTO로 바로 조회</li>
<li>패키지 명을 포함한 전체 클래스 명 일벽</li>
<li>순서와 타입이 일치하는 생성자 필요</li>
</ul>
<h1 id="💡페이징-api">💡페이징 API</h1>
<blockquote>
<ul>
<li>JPA는 페이징을 다음 두 API로 추상화함</li>
</ul>
</blockquote>
<ul>
<li><code>setFirstResult(int startPosition)</code> : 조회 시작 위치(0부터 시작)</li>
<li><code>setMaxResults(int maxResult)</code> : 조회할 데이터 수</li>
<li>persistence의 dialect에 설정된 db에 맞게 페이징 SQL을 작성해준다.</li>
</ul>
</li>
</ul>
<pre><code class="language-java">em.createQuery(&quot;select m from Member m order by m.age desc&quot;, Member.class)
    .setFirstResult(0)
    .setMaxResults(10)
    .getReulstList();</code></pre>
<pre><code class="language-sql">/*MYSQL 방언*/
SELECT
        M.ID AS ID,
        M.AGE AS AGE,
        M.TEAM_ID AS TEAM_ID,
        M.NAME AS NAME
FROM
        MEMBER M
ORDER BY
        M.NAME DESC LIMIT ?,?

/*Oracle 방언*/
SELECT *
FROM (SELECT ROW_.*, ROWNUM ROWNUM_
    FROM (SELECT M.ID      AS ID,
                M.AGE     AS AGE,
                M.TEAM_ID AS TEAM_ID,
                M.NAME    AS NAME
            FROM MEMBER M
            ORDER BY M.NAME
           ) ROW_
      WHERE ROWNUM &lt;= ?
     )
WHERE ROWNUM_ &gt; ?</code></pre>
<h1 id="💡조인">💡조인</h1>
<blockquote>
<ul>
<li>내부 조인</li>
</ul>
</blockquote>
<pre><code class="language-sql">SELECT m FROM Member m [INNER] JOIN m.team t</code></pre>
<ul>
<li>외부 조인<pre><code class="language-sql">SELECT m FROM Member m LEFT [OUTER] JOIN m.team t</code></pre>
</li>
<li>세타 조인<pre><code class="language-sql">SELECT count(m) FROM Member m, Team t WHERE m.username = t.name</code></pre>
</li>
</ul>
<h2 id="📗조인---on절">📗조인 - ON절</h2>
<h3 id="1-조인-대상-필터링">1. 조인 대상 필터링</h3>
<p>ex)회원과 팀을 조인하면서, 팀 이름이 A인 팀만 조인</p>
<ul>
<li>JPQL : SELECT m, t FROM Member m LEFT JOIN m.team t ON t.name = 'A'</li>
<li>SQL : SELECT m.*, t.* FROM Member m LEFT JOIN Team t ON m.TEAM_ID = t.id and t.name='A'<h3 id="2-연관관계-없는-엔티티-외부-조인">2. 연관관계 없는 엔티티 외부 조인</h3>
ex)회원의 이름과 팀의 이름이 같은 대상 외부 조인<ul>
<li>JPQL : SELECT m, t FROM Member m LEFT JOIN Team t ON m.username = t.name</li>
<li>SQL : SELECT m.*, t.* FROM Member m LEFT JOIN Team t ON m.username = t.name</li>
</ul>
</li>
</ul>
<h1 id="💡서브쿼리">💡서브쿼리</h1>
<blockquote>
<p>ex)나이가 평균보다 많은 회원</p>
</blockquote>
<pre><code class="language-sql">select m from Member m
where m.age &gt; (select avg(m2.age) from Member m2);</code></pre>
<p>ex)한 건이라도 주문한 고객</p>
<pre><code class="language-sql">select m from Member m
where (select count(o) from Order o where m=o.member)&gt;0</code></pre>
<h2 id="📗서브쿼리-지원-함수">📗서브쿼리 지원 함수</h2>
<ul>
<li>[NOT] EXISTS (subquery) : 서브쿼리에 결과가 존재하면 참<ul>
<li>{ALL | ANY | SOME} (subquery)</li>
<li>ALL : 모두 만족하면 참</li>
<li>ANY, SOME : 같은 의미, 조건을 하나라도 만족하면 참</li>
</ul>
</li>
<li>[NOT] IN (subquery) : 서브쿼리의 결과 중 하나라도 같은 것이 있으면 참</li>
</ul>
<pre><code class="language-sql">/*어떤 팀이든 팀에 소속된 회원*/
select m from Member m where exists(select t from m.team where t.name='팀A')

/*전체 상품 각각의 재고보다 주문량이 많은 주문들*/
select o from Order o
where o.orderAmout &gt; ALL (select p.stockAmount from Product p)

/*어떤 팀이든 팀에 소속된 회원*/
select m from Member m
where m.team = ANY(select t from Team t)</code></pre>
<h2 id="📗jpa-서브-쿼리-한계">📗JPA 서브 쿼리 한계</h2>
<ul>
<li>JPA는 WHERE, HAVING 절에서만 서브 쿼리 사용 가능</li>
<li>SELECT 절도 가능(하이버네이트에서 지원)</li>
<li>*<em>FROM 절의 서브쿼리는 현재 JPQL에서 불가능 *</em><ul>
<li><strong>조인으로 풀 수 있으면 풀어서 해결</strong></li>
</ul>
</li>
</ul>
<blockquote>
<p>📌하이버네이트6 변경사항
하이버네이트6 부터는 <strong>FROM 절의 서브쿼리를 지원</strong>한다!</p>
</blockquote>
<h1 id="💡jpql-타입-표현과-기타식">💡JPQL 타입 표현과 기타식</h1>
<blockquote>
<p>JPQL 타입 표현과 기타식</p>
</blockquote>
<h2 id="📗jpql의-타입-표현">📗JPQL의 타입 표현</h2>
<ul>
<li>문자 : 'HELLO', 'SHE''S'</li>
<li>숫자 : 10L(Long), 10D(Double), 10F(Float)</li>
<li>Boolean : TRUE, FALSE</li>
<li>ENUM : jpabook.MemberType.Admin (패키지명 포함)<pre><code class="language-sql">  selet m.username, 'HELLO', true from Member m
 where m.type = jpql.MemberType.ADMIN</code></pre>
</li>
<li>엔티티 타입 : TYPE(m) = Member (상속관계에서 사용)<pre><code class="language-java">  em.createQuery(&quot;select i from Item i where type(i) = Book&quot;, Item.class);</code></pre>
</li>
</ul>
<h2 id="📗jpql-기타">📗JPQL 기타</h2>
<ul>
<li>SQL과 문법이 같은 식</li>
<li>EXISTS, IN</li>
<li>AND, OR, NOT</li>
<li>=, &gt;, &gt;=, &lt;, &lt;=, &lt;&gt;</li>
<li>BETWEEN, LIKE, IS NULL</li>
</ul>
<h1 id="💡조건식">💡조건식</h1>
<blockquote>
<ul>
<li>CASE식</li>
</ul>
</blockquote>
<h2 id="📗기본-case-식">📗기본 CASE 식</h2>
<pre><code class="language-java">    //나이가 10보다 작거나 같으면 학생요금, 60보다 크거나 같으면 경로요금, 둘다 아니면 일반요금 출력
    select case when m.age &lt;= 10 then '학생요금'
                when m.age &gt;= 60 then '경로요금'
                else '일반요금'
                end
    from Member m</code></pre>
<h2 id="📗단순-case-식">📗단순 CASE 식</h2>
<pre><code class="language-java">    //팀 이름이 팀A면 인센티브 110%, 팀B면 인센티브 120%, 둘다 아니면 인센티브 105% 출력
    select case t.name
                    when '팀A' then '인센티브 110%'
                    when '팀B' then '인센티브 120%'
                    else '인센티브 105%'
                    end
    from Team t</code></pre>
<ul>
<li>COALESCE : 하나씩 조회해서 null이 아니면 반환<pre><code class="language-java">  //사용자 이름이 없으면 '이름 없는 회원'을 반환
  select coalesce(m.username, '이름 없는 회원') from Member m;</code></pre>
</li>
<li>NULLIF : 두 값이 같으면 null 반환, 다르면 첫번째 값 반환<pre><code class="language-java">  //사용자 이름이 `관리자`면 null을 반환하고 나머지는 본인 이름을 반환
  select NULLIF(m.username, '관리자') from Member ;</code></pre>
</li>
</ul>
<h1 id="💡jpql-기본-함수">💡JPQL 기본 함수</h1>
<ul>
<li>CONCAT</li>
<li>SUBSTRING</li>
<li>TRIM</li>
<li>LOWER, UPPER</li>
<li>LENGTH</li>
<li>LOCATE</li>
<li>ABS, SQRT, MOD</li>
<li>SIZE INDEX(JPA용도)<pre><code class="language-java">//- CONCAT
select concat('a','b'); //ab
</code></pre>
</li>
</ul>
<p>//- SUBSTRING: firstParam의 값을 secondParam위치부터 thirdParam갯수만큼 잘라서 반환
select substring('abcd', 2,3) // bc</p>
<p>//- TRIM
select trim(' lee han sol ')//lee han sol</p>
<p>//- LOWER, UPPER
select LOWER('hansolHI');//hansolhi
select UPPER('hansolHI');//HANSOLHI</p>
<p>//- LENGTH
select LENGTH('hansolHI'); // 6</p>
<p>//- LOCATE
select LOCATE('so', 'hansol');//4</p>
<p>//- ABS, SQRT, MOD
select ABS(-30);// 30
select SQRT(4);//2
select MOD(4,2);//0</p>
<p>//- SIZE, INDEX(JPA용도)
select SIZE(t.members) from Team t // 0 </p>
<pre><code>
# 💡사용자 정의 함수 호출
- 하이버네이트는 사용전 방언에 추가해야한다.
  - 사용하는 DB방언을 상속받고, 사용자 정의 함수를 등록한다
```java
//group_concat이라는 함수를 만들어서 등록한다고 가정한다.
public class MyPostgresDialect extends PostgreSQL94Dialect {
    public MyPostgresDialect() {
        registerFunction(&quot;group_concat&quot;, new StandardSQLFunction(&quot;group_concat&quot;, StandardBasicTypes.STRING));
    }
...
...

//설정파일 등록
&lt;property name=&quot;hibernate.dialect&quot; value=&quot;jpql.MyPostgresDialect&quot;/&gt;</code></pre>