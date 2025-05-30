<h1 id="💡영속성-컨텍스트">💡영속성 컨텍스트</h1>
<blockquote>
<ul>
<li>jpa를 이해하는데 가장 중요한 용어</li>
</ul>
</blockquote>
<ul>
<li><p><code>엔티티를 영구 저장하는 환경</code>이라는 뜻</p>
<ul>
<li><code>EntityManager.persist(entity);</code></li>
<li>엔티티 매니저를 통해 영속성 컨텍스트에 접근한다.</li>
</ul>
<img src="https://velog.velcdn.com/images/dev_ssj/post/ea89ed7c-7f14-4b69-a696-261537a144d7/image.png" />

</li>
</ul>
<h1 id="💡엔티티의-생명-주기">💡엔티티의 생명 주기</h1>
<h2 id="📗1-비영속newtransient">📗1. 비영속(new/transient)</h2>
<blockquote>
<p>영속성 컨텍스트와 전혀 관계가 없는 <code>새로운</code> 상태</p>
</blockquote>
<img src="https://velog.velcdn.com/images/dev_ssj/post/b20b14e6-2a7f-4bda-807c-c8f5c8843376/image.png" />

<pre><code class="language-java">    //비영속
    Member member = new Member();
    member.setId(101L);
    member.setName(&quot;HelloHJPA&quot;);</code></pre>
<ul>
<li>member 객체를 생성했을 뿐, 영속성 컨텍스트에 관리 되는 상태는 아니다.</li>
</ul>
<h2 id="📗2-영속managed">📗2. 영속(managed)</h2>
<blockquote>
<p>영속성 컨텍스트에 관리되는 상태</p>
</blockquote>
<img src="https://velog.velcdn.com/images/dev_ssj/post/ef06ac7a-e161-4c8f-90e3-6ddd3da7474d/image.png" />

<pre><code class="language-java">    //비영속
    Member member = new Member();
    member.setId(101L);
    member.setName(&quot;HelloHJPA&quot;);

    //영속
    em.persist(member);</code></pre>
<ul>
<li><code>em.persist(member);</code>로 member객체를 영속성 컨텍스트에 저장함.</li>
</ul>
<h2 id="📗3-준영속detached">📗3. 준영속(detached)</h2>
<blockquote>
<p>영속성 컨텍스트에 저장되었다가 분리된 상태</p>
</blockquote>
<pre><code class="language-java">    em.detach(member);</code></pre>
<ul>
<li>member 객체를 <code>em.detach(member);</code>로 영속성 컨텍스트에서 분리함.</li>
</ul>
<h2 id="📗4-삭제remove">📗4. 삭제(remove)</h2>
<blockquote>
<p>삭제된 상태</p>
</blockquote>
<pre><code class="language-java">    em.remove(member);</code></pre>
<h1 id="💡영속성-컨텍스트의-이점">💡영속성 컨텍스트의 이점</h1>
<h2 id="📗1-1차-캐시">📗1. 1차 캐시</h2>
<img src="https://velog.velcdn.com/images/dev_ssj/post/df96b431-fe71-4779-9a49-d6bbcd629aa6/image.png" />

<ul>
<li><code>em.persist(member);</code>를 할 때, JPA는 1차 캐시에 member1가 들어있는지 확인한다.</li>
<li>1차 캐시에 들어가 있지 않으면, 1차캐시에 member1을 저장한다.</li>
</ul>
<img src="https://velog.velcdn.com/images/dev_ssj/post/966afb58-8eed-4377-b359-57a45c7e1376/image.png" />

<ul>
<li><code>em.find()</code>로 member1을 조회할 때, JPA는 제일 먼저 1차 캐시를 확인하고, 1차 캐시에 member1이 있으면 1차 캐시에서 조회한다.<img src="https://velog.velcdn.com/images/dev_ssj/post/afc5b96c-fc6b-415b-8eb0-3309065575ba/image.png" />

</li>
</ul>
<ul>
<li>member2를 조회했을 때, JPA는 제일먼저 1차 캐시를 확인한다.</li>
<li>1차 캐시에 없다면 DB를 조회하게 된다.</li>
<li>DB에서 찾은 member2를 1차 캐시에 저장 후, 반환한다.</li>
</ul>
<h2 id="📗2-영속-엔티티의-동일성-보장">📗2. 영속 엔티티의 동일성 보장</h2>
<pre><code class="language-java">Member a = em.find(Member.class, &quot;member1);
Member b = em.find(Member.class, &quot;member2);

System.out.println(a == b); //true</code></pre>
<ul>
<li>JPA는 같은 레퍼런스의 객체면 엔티티의 동일성을 보장해준다.<h2 id="📗3-트랜잭션을-지원하는-쓰기-지연">📗3. 트랜잭션을 지원하는 쓰기 지연</h2>
<img src="https://velog.velcdn.com/images/dev_ssj/post/b16d8c2c-10e5-44b9-a8d3-a680d05e85d3/image.png" />

</li>
</ul>
<img src="https://velog.velcdn.com/images/dev_ssj/post/2930e6c8-715b-4052-a390-4993d7c400d2/image.png" />

<blockquote>
<p>(1) memberA가 1차 캐시에 저장된다.
(2) 동시에 JPA가 해당 엔티티를 분석해서 <code>INSERT SQL</code>을 생성
(3) 생성한 INSERT SQL을 <code>쓰기 지연 SQL 저장소</code>에 담아둔다.
(4) 멤버 B도 1차 캐시에 저장된다.
(5) 멤버 A와 마찬가지로 JPA가 <code>INSERT SQL</code>을 생성
(6)생성한 INSERT SQL을 <code>쓰기 지연 SQL 저장소</code>에 차곡차곡 담아둔다.
(7)<code>쓰기 지연 SQL 저장소</code>에 쌓아둔 쿼리들이 커밋이 되면 <code>fulsh</code>가 되면서 실제 데이터베이스 트랜잭션이 커밋됨.</p>
</blockquote>
<h2 id="📗4-변경-감지dirty-checking">📗4. 변경 감지(Dirty Checking)</h2>
<pre><code class="language-java">Member memberA = em.find(Member.class, &quot;memberA&quot;);

//영속 엔티티 데이터 수정
memberA.setUserName(&quot;aaa&quot;);
memberA.setAge(&quot;10&quot;);</code></pre>
<ul>
<li>JPA는 마치 자바 컬렉션을 다루듯 객체를 다룬다.</li>
<li>위 코드와 같이 setter를 이용해서 데이터의 변경이 감지되면 JPA는 데이터를 자동으로 변경해준다.<img src="https://velog.velcdn.com/images/dev_ssj/post/8b84d1dd-d5ca-4f2e-9a23-776ee9f14f8b/image.png" />

</li>
</ul>
<blockquote>
<p>(1) 스냅샷이란 값을 읽어온 <code>최초 시점의 상태를 저장</code>하는 공간이다.
(2) JPA가 트랜잭션을 커밋하는 시점에 내부적으로 <code>flush()</code>가 호출되면서 <code>스냅샷</code>과 <code>엔티티</code>를 비교한다.
(3) <code>UPDATE SQL</code>을 생성 한 후 <code>쓰기 지연 SQL 저장소</code>에 담아둔다.
(4) UPDATE 쿼리를 데이터베이스에 반영하고 <code>커밋</code>을 한다.</p>
</blockquote>
<h1 id="💡플러시">💡플러시</h1>
<blockquote>
<p>영속성 컨텍스트의 변경 내용을 데이터베이스에 반영한다.</p>
</blockquote>
<h2 id="📗플러시가-발생하면">📗플러시가 발생하면?</h2>
<blockquote>
<ul>
<li>변경 감지</li>
</ul>
</blockquote>
<ul>
<li><p>수정된 엔티티 쓰기 지연 SQL 저장소에 등록</p>
</li>
<li><p>쓰기 지연 SQL 저장소의 쿼리를 데이터 베이스에 전송</p>
<h2 id="📗영속성-컨텍스트를-플러시하는-방법">📗영속성 컨텍스트를 플러시하는 방법</h2>
</li>
<li><p><code>em.flush()</code> : 직접 호출</p>
</li>
<li><p><code>트랜잭션 커밋</code> : 플러시 자동 호출</p>
</li>
<li><p><code>JPQL 쿼리 실행</code> : 플러시 자동 호출</p>
</li>
</ul>
<blockquote>
<p>📌<strong>플러시는!</strong></p>
</blockquote>
<ul>
<li>영속성 컨텍스트를 비우지 않는다.</li>
<li>영속성 컨텍스트의 변경 내용을 <code>데이터 베이스에 동기화</code> 해준다.</li>
<li><code>트랜잭션</code>이라는 작업 단위가 중요
➡️커밋 직전에만 동기화하면 된다!</li>
</ul>
<h1 id="💡준영속-상태">💡준영속 상태</h1>
<blockquote>
<ul>
<li>영속 -&gt; 준영속</li>
</ul>
</blockquote>
<ul>
<li><p>영속 상태의 엔티티가 영속성 컨텍스트에서 분리 <code>detached</code></p>
</li>
<li><p>영속성 컨텍스트가 제공하는 기능 사용 불가</p>
<h2 id="📗준영속-상태로-만드는-법">📗준영속 상태로 만드는 법</h2>
<ul>
<li><code>em.detach(entity)</code> : <strong>특정 엔티티</strong>만 준영속 상태로 전환</li>
<li><code>em.celar()</code> : 영속성 컨텍스트를 <strong>완전히 초기화</strong></li>
<li><code>em.close()</code> : 영속성 컨텍스트를 <strong>종료</strong></li>
</ul>
</li>
</ul>