<h1 id="💡값-타입-컬렉션">💡값 타입 컬렉션</h1>
<ul>
<li><code>값 타입을 컬렉션으로 넣어서 쓰는 것</code>을 의미</li>
<li><code>@ElementCollection</code>, <code>@CollectionTable</code> 사용<ul>
<li><code>@ElementCollection</code> : 컬렉션 객체임을 JPA가 알 수 있게 해준다</li>
<li><code>@CollectionTable</code> : 값 타입 컬렉션을 매핑할 테이블에 대한 역할을 지정하는 데 사용한다.</li>
</ul>
</li>
<li>데이터베이스는 컬렉션을 같은 테이블에 저장할 수 없다.<ul>
<li>일대다로 풀어서 별도의 테이블로 만들어야 한다.</li>
</ul>
</li>
<li>컬렉션을 저장하기 위한 <code>별도의 테이블</code>이 필요<ul>
<li>즉, 조인키가 필요하다<img src="https://velog.velcdn.com/images/dev_ssj/post/a82738d6-0e2a-4e84-89c6-54df1357047d/image.png" />


</li>
</ul>
</li>
</ul>
<h2 id="📗값-타입-컬렉션-사용">📗값 타입 컬렉션 사용</h2>
<h3 id="📌값-타입-저장-예제">📌값 타입 저장 예제</h3>
<blockquote>
<p>좋아하는 음식들을 저장하는 favoriteFoods 주소내역을 저장하는 addressHistory 구현해보자. </p>
</blockquote>
<h4 id="memberjava">member.java</h4>
<pre><code class="language-java">...
//favoriteFoods
//값 타입 컬렉션 지정
@ElementCollection
@CollectionTable(name=&quot;FAVORITE_FOOD&quot;, joinColumns =
                    @JoinColumn(name=&quot;MEMBER_ID&quot;))    //조인할 컬럼명 지정
                       @Column(name=&quot;FOOD_NAME&quot;)        //컬럼명 지정
private Set&lt;String&gt; favoriteFoods = new HashSet&lt;&gt;();

//addressHistory
//값 타입 컬렉션 지정
@ElementCollection
@CollectionTable(name=&quot;ADDRESS&quot;, joinColumns = 
                    @JoinColumn(name=&quot;MEMBER_ID&quot;))
private List&lt;Address&gt; addressHistory = new ArrayList&lt;&gt;();</code></pre>
<ul>
<li><code>@ElementCollection</code> : 컬렉션 객체임을 JPA가 알 수 있게 해준다. 엔티티가 아닌 값 타입, 임베디드 타입에 대한 테이블을 생성하고 1대다 관계로 다룬다.</li>
<li><code>@CollectionTable</code> : 값 타입 컬렉션을 매핑할 테이블에 대한 역할을 지정하는 데 사용한다. 테이블의 이름과 조인정보를 적어줘야 한다.</li>
</ul>
<h4 id="값-저장">값 저장</h4>
<pre><code class="language-java">Member member1 = new Member();
member1.setUsername(&quot;member1&quot;);

member1.setHomeAddress(new Address(&quot;city1&quot;,&quot;Street1&quot;, &quot;1000&quot;));

member1.getFavoriteFoods().add(&quot;치킨&quot;);
member1.getFavoriteFoods().add(&quot;피자&quot;);
member1.getFavoriteFoods().add(&quot;족발&quot;);

member1.getAddressHistory().add(new Address(&quot;old1&quot;,&quot;Street1&quot;, &quot;1000&quot;));
member1.getAddressHistory().add(new Address(&quot;old2&quot;,&quot;Street2&quot;, &quot;1000&quot;));

//멤버만 persist
em.persist(member1);</code></pre>
<img src="https://velog.velcdn.com/images/dev_ssj/post/99385a2f-ef7b-4707-a39e-1dfd5fa69506/image.png" />

<ul>
<li>member1객체만 persist했음에도 FAVORITE_FODD테이블과 ADDRESS테이블에도 컬렉션에 저장한 값이 들어가는걸 확인할 수 있다.</li>
</ul>
<h3 id="📌값-타입-조회-예제">📌값 타입 조회 예제</h3>
<pre><code class="language-java">//멤버만 불러와짐. 컬렉션타입 : 기본값은 지연로딩
System.out.println(&quot;==================== START ====================&quot;);
Member findMember = em.find(Member.class, member1.getId());</code></pre>
<img src="https://velog.velcdn.com/images/dev_ssj/post/44d1994d-52b7-40f5-a2c4-d417ef7918d5/image.png" />

<ul>
<li>컬렉션 값 타입의 @ElementCollection은 LAZY로 설정돼 있어 지연로딩으로 작동한다.<ul>
<li>em.find()로 member를 호출하면 member의 값들만 호출된다.</li>
</ul>
</li>
</ul>
<h3 id="📌값-타입-수정-예제">📌값 타입 수정 예제</h3>
<pre><code class="language-java">//setter 사용X. 사이드이펙트 생길수 있다.
//findMember.getHomeAddress().setCity(&quot;newCity&quot;);

// 치킨 -&gt; 한식
member.getFavoriteFoods().remove(&quot;치킨&quot;);
member.getFavoriteFoods().add(&quot;한식&quot;);

// 주소변경
member.getAddressHistory().remove(new Address(&quot;oldCity&quot;, &quot;street&quot;, &quot;10000&quot;));
member.getAddressHistory().add(new Address(&quot;newCity&quot;, &quot;street&quot;, &quot;10000&quot;));</code></pre>
<ul>
<li>컬렉션 값 타입을 수정할 때 setter를 사용하면 사이드 이펙트 발생 가능성이 있으므로 사용하면 안된다.</li>
<li>값 타입 컬렉션 안의 데이터를 수정할 때는 일부만 수정하는 것이 아닌 <code>데이터를 삭제 후 변경된 데이터 전체를 새로 추가</code>해줘야 한다.</li>
</ul>
<h2 id="📗값-타입-제약사항">📗값 타입 제약사항</h2>
<ul>
<li>값 타입은 엔티티와 다르게 식별자 개념이 없다.</li>
<li>값은 변경하면 추적이 어렵다.</li>
<li>값 타입 컬렉션에 변경 사항이 발생하면, 주인 엔티티와 연관된 모든 데이터를 삭제하고, 값 타입 컬렉션에 있는 현재 값을 모두 다시 저장한다.</li>
<li>값 타입 컬렉션을 매핑하는 테이블은 모든 컬럼을 묶어서 기본 키를 구성해야 함 -&gt; null 입력X, 중복 저장X</li>
</ul>
<pre><code class="language-java">//값 타입 2개 add
member.setAddressHistory.add(new Address(&quot;oldCity1&quot;, &quot;street&quot;, &quot;10000&quot;);
member.setAddressHistory.add(new Address(&quot;oldCity2&quot;, &quot;street&quot;, &quot;10000&quot;);

member.getAddressHistory().remove(new Address(&quot;oldCity1&quot;, &quot;street&quot;, &quot;10000&quot;));
member.getAddressHistory().add(new Address(&quot;newCity&quot;, &quot;street&quot;, &quot;10000&quot;));</code></pre>
<blockquote>
<ul>
<li>값 타입 컬렉션에 Address 데이터가 2개 저장되어 있는 상황에서 기존에 있던 데이터 1개를 삭제하고 새로운 데이터를 추가해 주었다.</li>
</ul>
</blockquote>
<ul>
<li>우리가 기대하는 SQL은 delete 쿼리 1개와 insert 쿼리 1개이다.</li>
<li>하지만 값 타입 컬렉션은 주인 엔티티와 연관된 모든 데이터를 삭제하고 다시 저장하기 때문에 Member에 연관된 oldCity1, oldCity2를 모두 삭제하고 oldCity2와 newCity를 insert 한다.
➡️따라서 delete 쿼리 1개와 insert 쿼리 2개가 DB로 나가게 된다.</li>
</ul>
<h2 id="📗값-타입-컬렉션-대안">📗값 타입 컬렉션 대안</h2>
<ul>
<li><p>실무에서는 상황에 따라 <strong>값 타입 컬렉션 대신 일대다 관계를 고려</strong>한다</p>
</li>
<li><p>일대다 관계를 위한 엔티티를 만들고, 여기에서 값 타입을 사용한다.</p>
</li>
<li><p><code>영속성 전이(Cascade) + 고아 객체 제거</code>를 사용해서 값 타입 컬렉션 처럼 사용</p>
<h3 id="adressentityjava">AdressEntity.java</h3>
<pre><code class="language-java">@Entity
public class AddressEntity {
  @Id @GeneratedValue
  private Long id;
  private Address address;

  public AddressEntity(String city, String street, String zipcode ) {
    this.address = new Address(city, street, zipcode);
}

public AddressEntity() {
}
</code></pre>
</li>
</ul>
<p>}</p>
<pre><code>
### Member.java
```java
@Entity
public class Member {
...
@OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)
@JoinColumn(name = &quot;MEMBER_ID&quot;)
private List&lt;AddressEntity&gt; addressHistory = new ArrayList&lt;&gt;();
...
}</code></pre><blockquote>
<ul>
<li>Address라는 값 타입을 감싸는 AddressEntity를 새롭게 만들고 저장하는 방식</li>
</ul>
</blockquote>
<ul>
<li>cascade를 ALL, orphanRemoval을 true로 설정해 주면 주인 엔티티에 의존하면서 기존의 제약사항을 해결할 수 있다.</li>
</ul>