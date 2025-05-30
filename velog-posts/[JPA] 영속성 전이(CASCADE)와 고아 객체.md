<h1 id="💡영속성-전이cascade">💡영속성 전이(CASCADE)</h1>
<blockquote>
<p>특정 엔티티를 영속 상태로 만들 때, 연관된 엔티티도 함께 영속 상태로 만들고 싶을 때 사용
<code>ex) 부모 엔티티를 저장할 때 자식 엔티티도 함께 저장</code></p>
</blockquote>
<img src="https://velog.velcdn.com/images/dev_ssj/post/1197d451-7c41-42db-a095-811e74eff055/image.png" />


<h2 id="📗영속성-전이를-쓰지-않는다면">📗영속성 전이를 쓰지 않는다면?</h2>
<blockquote>
<p>부모엔티티를 저장할 때 자식 엔티티도 함께 저장하고싶다.
이 상황에서 영속성 전이를 쓰지 않는다면?</p>
</blockquote>
<h3 id="parentjava">Parent.java</h3>
<pre><code class="language-java">@Entity
public class Parent {
    @Id @GeneratedValue
    private Long id;

    private String name;

    @OneToMany(mappedBy = &quot;parent&quot;)
    private List&lt;Child&gt; childList = new ArrayList&lt;&gt;();
    ...</code></pre>
<h3 id="childjava">Child.java</h3>
<pre><code class="language-java">@Entity
public class Child {
    @Id
    @GeneratedValue
    private Long id;

    private String name;

    @ManyToOne
    @JoinColumn(name=&quot;parent_id&quot;)
    private Parent parent;
    ...
}</code></pre>
<h3 id="부모-자식-값-저장">부모, 자식 값 저장</h3>
<pre><code class="language-java">Child child1 = new Child();
Child child2 = new Child();

Parent parent = new Parent();
parent.addChild(child1);
parent.addChild(child2);

//부모 
em.persist(parent);
//자식1
em.persist(child1);
자식2 
em.persist(child2);
</code></pre>
<blockquote>
<ul>
<li><code>JPA에서 엔티티를 저장할 때 연관된 모든 엔티티는 영속상태여야 한다.</code></li>
</ul>
</blockquote>
<ul>
<li>즉, 부모 엔티티를 영속 상태로 만들고, 자식 엔티티도 각각 영속상태로 만들어야한다.
➡️<strong>이럴 때, 영속성 전이를 사용하면 부모 엔티티만 영속 상태로 만들어두면 연관된 자식들을 한번에 영속 상태로 만들 수 있다.</strong></li>
</ul>
<h2 id="📗영속성-전이---저장">📗영속성 전이 - 저장</h2>
<h3 id="parentjava-1">Parent.java</h3>
<pre><code class="language-java">@Entity
public class Parent {
    @Id @GeneratedValue
    private Long id;

    private String name;
    //영속성 전이(CASCADE)속성 부여
    @OneToMany(mappedBy = &quot;parent&quot;, cascade = CascadeType.ALL)
    private List&lt;Child&gt; childList = new ArrayList&lt;&gt;();
    ...</code></pre>
<h3 id="childjava-1">Child.java</h3>
<pre><code class="language-java">@Entity
public class Child {
    @Id
    @GeneratedValue
    private Long id;

    private String name;

    @ManyToOne
    @JoinColumn(name=&quot;parent_id&quot;)
    private Parent parent;
    ...
}</code></pre>
<h3 id="부모-자식-값-저장-1">부모, 자식 값 저장</h3>
<pre><code class="language-java">Child child1 = new Child();
Child child2 = new Child();

Parent parent = new Parent();
parent.addChild(child1);
parent.addChild(child2);

//부모엔티티를 영속상태로 만듦 -&gt; 자식엔티티까지 모두 영속상태로 만들어짐
em.persist(parent);
</code></pre>
<blockquote>
<p>📌<strong>주의할 점</strong></p>
</blockquote>
<ul>
<li>영속성 전이는 연관관계를 매핑하는 것과 아무 관련이 없음
➡️엔티티를 영속화할 때 연관된 엔티티도 함께 영속화하는 편리함을 제공할 뿐임</li>
<li><code>소유자가 하나</code>일때만 영속성 전이를 써야한다<ul>
<li>만약 Child가 Parent가 아닌 다른 엔티티와 연관관계가 있다면?
➡️<strong>사용 금지</strong></li>
</ul>
</li>
</ul>
<h2 id="📗cascade의-종류">📗CASCADE의 종류</h2>
<ul>
<li><code>ALL</code> : 모두 적용</li>
<li><code>PERSIST</code> : 영속</li>
<li><code>REMOVE</code> : 삭제</li>
<li><code>MERGE</code> : 병합</li>
<li><code>REPRESH</code> : REPRESH</li>
<li><code>DETACH</code> : DETACH</li>
</ul>
<h1 id="💡고아-객체">💡고아 객체</h1>
<blockquote>
<p> 부모 엔티티와 <code>연관관계가 끊어진 자식 엔티티</code>를 의미</p>
</blockquote>
<h2 id="📗고아-객체-제거">📗고아 객체 제거</h2>
<blockquote>
<ul>
<li>부모 엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제</li>
</ul>
</blockquote>
<ul>
<li><code>orphanRemoval = true</code></li>
</ul>
<h3 id="parentjava-2">Parent.java</h3>
<pre><code class="language-java">@Entity
public class Parent {
    @Id @GeneratedValue
    private Long id;

    private String name;

    //고아 객체 삭제 설정
    @OneToMany(mappedBy = &quot;parent&quot;, cascade = CascadeType.ALL, orphanRemoval = true)
    private List&lt;Child&gt; childList = new ArrayList&lt;&gt;();

    public void addChild(Child child){
        childList.add(child);
        child.setParent(this);
    }
...
}</code></pre>
<h3 id="childjava-2">Child.java</h3>
<pre><code class="language-java">@Entity
public class Child {
    @Id
    @GeneratedValue
    private Long id;

    private String name;

    @ManyToOne
    @JoinColumn(name=&quot;parent_id&quot;)
    private Parent parent;
    ...
}</code></pre>
<h3 id="자식-값-삭제">자식 값 삭제</h3>
<pre><code class="language-java">Child child1 = new Child();
Child child2 = new Child();

Parent parent = new Parent();
parent.addChild(child1);
parent.addChild(child2);

em.persist(parent);

em.flush();
em.clear();

Parent findParent = em.find(Parent.class, parent.getId());
findParent.getChildList().remove(0);</code></pre>
<h3 id="실행-결과">실행 결과</h3>
<img src="blob:https://velog.io/86bdf8a2-a63d-4de8-81fd-63d15b77012f" />


<blockquote>
<ul>
<li>컬렉션에서 빠진 0번째 자식은 DELETE 쿼리가 나가면서 삭제된다.</li>
</ul>
</blockquote>
<h2 id="📗주의할-점">📗주의할 점</h2>
<ul>
<li>참조가 제거된 엔티티는 <code>다른 곳에서 참조하지 않는 고아 객체</code>로 보고 삭제하는 기능</li>
<li><code>참조하는 곳이 하나</code>일 때 사용해야함</li>
<li>특정 엔티티가 <code>개인 소유</code>할 때 사용</li>
<li><code>@OneToOne</code>, <code>@OneToMany</code>만 가능</li>
</ul>
<blockquote>
<p>📌<strong>참고</strong></p>
</blockquote>
<ul>
<li>개념적으로 부모를 제거하면 자식은 고아가 된다.</li>
<li>따라서 객체 제거 기능을 활성화하면, <code>부모를 제거할 때 자식도 함께 제거</code>된다.<ul>
<li>Parent가 삭제되면 자식 객체인 Child도 삭제된다. </li>
</ul>
</li>
<li>이것은 CascadeType.REMOVE처럼 동작한다.</li>
</ul>
<h2 id="📗영속성-전이--고아객체-생명주기">📗영속성 전이 + 고아객체, 생명주기</h2>
<blockquote>
<p><strong>CascadeType.ALL + orphanRemovel=true</strong></p>
</blockquote>
<ul>
<li>스스로 생명주기를 관리하는 엔티티는 <code>em.persist()</code>로 영속화, <code>em.remove()</code>로 제거한다.</li>
<li>영속성 전이 + 고아객체 두 옵션을 모두 활성화하면 부모 엔티티를 통해서 자식의 생명주기를 관리할 수 있다.<ul>
<li><code>em.persist(parent)</code> ➡️ 부모 엔티티를 영속화했지만 자식 객체도 같이 영속화됨</li>
<li><code>em.remove(findParent)</code> ➡️ 부모 엔티티를 삭제했지만 자식 객체도 같이 삭제됨</li>
<li><code>findParent.getChildList().remove(0)</code> ➡️ 부모객체에서 자식객체를 꺼내서 자식객체를 삭제함</li>
</ul>
</li>
<li>도메인 주도 설계(DDD)의 Aggregate Root 개념을 구현할 때 유용</li>
</ul>