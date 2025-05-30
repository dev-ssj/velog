<h1 id="💡페치-조인fetch-join">💡페치 조인(fetch join)</h1>
<blockquote>
<p><strong>실무에서 자주 사용되며 정말 중요하다</strong></p>
</blockquote>
<ul>
<li>SQL 조인 종류는 아니고 JPA에서 제공하는 기능</li>
<li>JPQL에서 <code>성능 최적화</code>를 위해 제공하는 기능</li>
<li>연관된 엔티티나 컬렉션을 <code>SQL 한번에 함께 조회</code>하는 기능</li>
<li>join fetch 명령어 사용</li>
<li>페치 조인 :: = [LEFT [OUTER]|INNER]JOIN FETCH 조인경로</li>
</ul>
<h2 id="📗엔티티-페치-조인">📗엔티티 페치 조인</h2>
<ul>
<li>회원을 조회하면서 <code>연관된 팀도 함께 조회</code>(SQL 한번에)</li>
<li>SQL을 보면 회원 뿐만 아니라 팀(T.*)도 함께 SELECT<pre><code class="language-sql">//JPQL
select m from Member m join fetch m.team
</code></pre>
</li>
</ul>
<p>//SQL
select m.* t.* from Member m inner join Team t on m.team_id = t.id;</p>
<pre><code>
&lt;img src = https://velog.velcdn.com/images/dev_ssj/post/893ed4cd-bbb4-44a3-8bee-6e4f3e50a60f/image.png&gt;
- 팀이 없는 회원을 조회하고 싶을 때 fetch 조인을 사용하면 내부적으로 inner join을 사용한다.
- 팀이 없는 회원은 누락된다.

## 📗페치 조인 사용 코드
- 기존 연관관계 조회 로직의 문제점
```java
String jpql = &quot;select m from Member m&quot;;
List&lt;Member&gt; members = em.createQuery(jpql, Member.class) 
                        .getResultList();
for (Member member : members) {
    System.out.println(&quot;username = &quot; + member.getUsername() + &quot;, &quot; +
                        &quot;teamName = &quot; + member.getTeam().name());
    //회원1, 팀A(SQL)
    //회원2, 팀A(1차 캐시)
    //회원3, 팀B(SQL)

    //회원 100명 -&gt; N + 1
}</code></pre><blockquote>
<p>최초 jpql을 통해 Member를 조회할 때, Team의 정보를 프록시로 가져온다.
getTeam(), getName()을 통해 팀의 정보를 조회하려고 할때, 실제 객체를 SQL을 수행하여 가져온다.<br />
한번 가져온 Team의 정보는 1차 캐시에 올라가 있으므로 다시 조회할 필요는 없지만, 회원 N명을 조회할 때 최대 <strong>N+1</strong>번의 쿼리가 수행될 수 있다.</p>
</blockquote>
<ul>
<li>페치 조인을 통해 해결<pre><code class="language-java">String jpql = &quot;select m from Member m join fetch m.team&quot;;
List&lt;Member&gt; members = em.createQuery(jpql, Member.class) 
                                               .getResultList();
for (Member member : members) {
  //페치 조인으로 회원과 팀을 함께 조회해서 지연 로딩X
  System.out.println(&quot;username = &quot; + member.getUsername() + &quot;, &quot; +
                               &quot;teamName = &quot; + member.getTeam().name());
}</code></pre>
<blockquote>
<p>페치 조인은 조회 당시에 실제 엔티티가 담기므로 지연로딩 없이 바로 사용이 가능하다.</p>
</blockquote>
</li>
</ul>
<h2 id="📗컬렉션-페치-조인">📗컬렉션 페치 조인</h2>
<ul>
<li>일대다 관계, 컬렉션 페치 조인<pre><code class="language-java">//JPQL
select t from Team t join fetch t.members where t.name = '팀A';
</code></pre>
</li>
</ul>
<p>//SQL
select t.<em>, m.</em> from team t, inner join member m on t.id = m.team_id
 where t.name = '팀A';</p>
<pre><code>➡️Team은 하나지만, Member가 1개 이상일 수도 있다.
&lt;img src = https://velog.velcdn.com/images/dev_ssj/post/deecae4b-babf-4e38-a1e3-9dbd87dba3a6/image.png&gt;
&gt; - 팀A는 1개이지만 그에 해당하는 멤버는 회원1과 회원2로 두명이므로 조회결과는 2개의 row가 된다.
 - 팀은 하나이기에 같은 주소값을 가진 결과가 두개 나오고, 팀 A의 입장에서는 회원1, 회원2를 총 두개를 가진다.

## 📗컬렉션 페치 조인 사용 코드
```java
String jpql = &quot;select t from Team t join fetch t.members where t.name = '팀A'&quot;
List&lt;Team&gt; teams = em.createQuery(jpql, Team.class).getResultList();
for (Team team : teams) {
    System.out.println(&quot;teamname = &quot; + team.getName() + &quot;, team = &quot; + team);
    for (Member member : team.getMembers()) {
        //페치 조인으로 팀과 회원을 함께 조회해서 지연 로딩 발생 안함
        System.out.println(&quot;-&gt;username = &quot; + member.getUsername()+ &quot;, member = &quot; + member);
    }
}</code></pre><img src="https://velog.velcdn.com/images/dev_ssj/post/5fe4856a-4f6a-48cf-bc0a-d21c297715fb/image.png" />

<h2 id="📗페치-조인과-distinct">📗페치 조인과 DISTINCT</h2>
<blockquote>
<p>일대다 관계에서는 join fetch 결과가 뻥튀기 될 수 있다.</p>
</blockquote>
<pre><code class="language-java">String query = &quot;select t from Team t&quot;;
String query2 = &quot;select t from Team t join fetch t.members&quot;;
List&lt;Team&gt; result = em.createQuery(query, Team.class).getResultList();

List&lt;Team&gt; result2 = em.createQuery(query2, Team .class).getResultList();

System.out.println(&quot;result size::&quot;+ result.size()); //2
System.out.println(&quot;result2 size::&quot;+ result2.size());//3</code></pre>
<img src="https://velog.velcdn.com/images/dev_ssj/post/2d4fc2c9-41ec-4158-a7cd-f4e602dcb414/image.png" />

<ul>
<li>SQL의 DISTINCE는 중복된 결과를 제거하는 명령</li>
<li>JPQL의 DISTINCT는 2가지 기능을 제공<ol>
<li>SQL에 DISTINCT를 추가</li>
<li>애플리케이션에서 엔티티 중복 제거<pre><code class="language-java">select distinct t from Team t join fetch t.members where t.name = '팀A';</code></pre>
</li>
</ol>
</li>
<li>위 코드를 실행하면 SQL에 DISTINCT를 추가하지만 ID(PK)도 다르고 NAME도 다르므로 중복제거 실패</li>
<li>쿼리만으로는 중복제거가 안되기 때문에 JPA 추가적으로 DISTINCT가 애플리케이션에서 중복 제거를 시도</li>
<li>같은 식별자를 가진 Team 엔티티 제거<img src="https://velog.velcdn.com/images/dev_ssj/post/f99caed8-ed87-41a7-98b3-0849cfd68b79/image.png" />

</li>
</ul>
<blockquote>
<p><strong>📌반대로 다대일은 뻥튀기 되지 않는다</strong></p>
</blockquote>
<blockquote>
<p><strong>📌하이버네이트6 변경 사항</strong></p>
</blockquote>
<ul>
<li><p>하이버네이트6 부터는 DISTINCT 명령어를 사용하지 않아도 애플리케이션에서 중복 제거가 자동으로 적용 된다.</p>
<h2 id="📗페치-조인과-일반조인의-차이">📗페치 조인과 일반조인의 차이</h2>
<ul>
<li>일반 조인 실행 시 연관된 엔티티를 함께 조회하지 않음.<pre><code class="language-java">//JPQL
select from Team t join t.members m where t.name = '팀A';
</code></pre>
</li>
</ul>
</li>
</ul>
<p>//SQL
select t.* from Team t inner join member m on t.id = m.team_id
 where t.name = '팀A';</p>
<pre><code> - 해당 쿼리 수행 시 일반 조인은 연관된 엔티티를 먼저 조회하지 않기 때문엔 프록시 객체를 반환한다.
 - `실제로 해당 엔티티를 사용할 때 실제 값을 조회`한다.
 - 반면, 페치 조인은 조회 시 연관관계도 함께 조회(`즉시 로딩`)
 - 페치 조인은 `객체 그래프를 SQL 한번에 조회하는 개념`이다.

## 📗페치 조인의 특징과 한계
- 페치 조인 대상에는 별칭을 줄 수 없다.
  - 하이버네이트는 가능하지만, 가급적 사용하지 않는게 좋다.
- 둘 이상의 컬렉션은 페치 조인 할 수 없다.
```java
    String query = &quot;select t from Team t join fetch t.members, t.orders&quot;
    //불가능. fetch join에서 컬렉션은 1개만 사용하자.</code></pre><ul>
<li>컬렉션을 페치 조인하면 페이징 API를 사용할 수 없다.<ul>
<li>일대일, 다디앨 같은 단일 값 연관 필드들은 페치 조인해도 페이징 가능</li>
<li>하이버네이트는 경고 로그를 남기고 메모리에서 페이징(매우 위험)</li>
</ul>
</li>
</ul>
<pre><code class="language-java">String query = &quot;select t from Team t join fetch t.members&quot;;
List&lt;Team&gt; result = em.createQuery(query, Team.class)
                        .setFirstResult(0)
                        .setMaxResults(1)
                        .getResultList();</code></pre>
<ul>
<li>연관된 엔티티들을 SQL 한번으로 조회 - 성능 최적화</li>
<li>엔티티에 직접 적용하는 글로벌 로딩 전략보다 우선함<ul>
<li>@OneToMany(fetch = FetchType.LAZY)//글로벌 로딩 전략</li>
</ul>
</li>
<li>실무에서 글로벌 로딩 전략은 모두 지연 로딩</li>
<li>최적화가 필요한 곳은 페치 조인 적용</li>
</ul>
<h2 id="📗페치-조인---정리">📗페치 조인 - 정리</h2>
<ul>
<li>모든 것을 페치 조인으로 해결 할 수는 없다.</li>
<li>페치 조인은 객체 그래프를 유지할 때 사용하면 효과적이다</li>
<li>여러 테이블을 조인해서 엔티티가 아닌 전혀 다른 결과를 내야 하면, 페치 조인 보다는 일반 조인을 사용하고 필요한 데이터들만 조회해서 DTO로 반환하는 것이 효과적이다.</li>
</ul>