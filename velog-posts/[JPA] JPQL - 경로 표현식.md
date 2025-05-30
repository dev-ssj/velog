<h1 id="💡경로-표현식">💡경로 표현식</h1>
<ul>
<li>.(점)을 찍어 객체 그래프를 탐색하는 것<pre><code class="language-java">select m.username -&gt; 상태 필드
  from Member m
  join m.team t -&gt; 단일 값 연관 필드
  join m.orders o -&gt; 컬렉션 값 연관 필드
where t.name ='팀A'</code></pre>
</li>
</ul>
<h2 id="📗경로-표현식-용어-정리">📗경로 표현식 용어 정리</h2>
<ul>
<li><code>상태 필드(state field)</code> : 단순히 값을 저장하기 위한 필드(ex:m.username)</li>
<li><code>연관 필드(association field)</code> : 연관관계를 위한 필드<ul>
<li><code>단일 값 연관 필드</code> : 
@ManyToOne, @OneToOne, 대상이 엔티티(ex:m.team)</li>
<li><code>컬렉션 값 연관 필드</code> : 
@OneToMany, @ManyToMany, 대상이 컬렉션(ex:m.orders)</li>
</ul>
</li>
</ul>
<h2 id="📗경로-표현식-특징">📗경로 표현식 특징</h2>
<ul>
<li>상태 필드(state field) : 경로 탐색의 끝, 탐색x<pre><code class="language-sql">  select m.username from Member m;</code></pre>
</li>
<li>단일 값 연관 경로 : 묵시적 내부 조인(inner join) 발생, 탐색O<pre><code class="language-sql">  select m.team.name from Member m; //team에서 경로탐색이 더 가능</code></pre>
</li>
<li>컬렉션 값 연관 경로 : 묵시적 내부 조인 발생, 탐색X <ul>
<li>FROM 절에서 명시적 조인을 통해 별칭을 얻으면 별칭을 통해 탐색 가능<pre><code class="language-sql">    select m.username from Team t join t.members m;</code></pre>
</li>
</ul>
</li>
</ul>
<h2 id="📗명시적-조인-묵시적-조인">📗명시적 조인, 묵시적 조인</h2>
<h3 id="명시적-조인">명시적 조인</h3>
<ul>
<li>join 키워드를 직접 사용하여 조인<ul>
<li>select m from Member m <strong>join</strong> m.team t</li>
</ul>
</li>
</ul>
<h3 id="묵시적-조인">묵시적 조인</h3>
<ul>
<li>경로 표현식에 의해 묵시적으로 SQL 조인 발생(내부 조인만 가능)<ul>
<li>select <strong>m.team</strong> from Member m
➡️from절에서 member와 팀을 조인</li>
</ul>
</li>
</ul>
<blockquote>
<p><strong><span style="color: red;">❌실무에서는 가급적 묵시적 조인 대신에 명시적 조인을 사용하자
조인은 SQL 튜닝에 중요 포인트
묵시적 조인은 조인이 일어나는 상황을 한눈에 파악하기 어렵다</strong></span></p>
</blockquote>