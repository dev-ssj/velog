<h1 id="💡jpa의-데이터-타입-분류">💡JPA의 데이터 타입 분류</h1>
<h2 id="📗1-엔티티-타입">📗1. 엔티티 타입</h2>
<ul>
<li>@Entity로 정의하는 객체</li>
<li>데이터가 변해도 식별자로 지속해서 추적 가능<ul>
<li>회원 엔티티의 키나 나이를 변경해도 식별자로 인식 가능</li>
</ul>
</li>
</ul>
<h2 id="📗2-값-타입">📗2. 값 타입</h2>
<ul>
<li>int, Integer, String처럼 단순히 값으로 사용하는 자바 기본 타입이나 객체</li>
<li>식별자가 없고 값만 있으므로 변경 시 추적 불가<ul>
<li>숫자 100을 200으로 변경하면 완전히 다른 값으로 대체</li>
</ul>
</li>
</ul>
<h2 id="📗값-타입의-분류">📗값 타입의 분류</h2>
<ul>
<li>기본 값 타입<ul>
<li>자바 기본 타입(int, double)</li>
<li>래퍼 클래스(Integer, Long)</li>
<li>String</li>
<li>임베디드 타입</li>
<li>복합 값 타입</li>
<li>컬렉션 값 타입  </li>
</ul>
</li>
</ul>
<h1 id="💡임베디드-타입embeddedtype">💡임베디드 타입(EmbeddedType)</h1>
<ul>
<li><code>복합 값 타입</code>을 의미</li>
<li>새로운 값 타입을 직접 정의할 수 있다.</li>
<li>JPA는 <code>임베디드 타입(embeddedType)</code>이라 한다.</li>
<li>주로 기본 값 타입을 모아서 만들어서 복합 값 타입이라고도 하며, int, String과 같은 값 타입을 의미한다.</li>
</ul>
<h2 id="📗임베디드-타입-사용법">📗임베디드 타입 사용법</h2>
<ul>
<li><p><code>@Embeddeble</code> : 값 타입을 정의하는 곳에 표시</p>
</li>
<li><p><code>@Embedded</code> : 값 타입을 사용하는 곳에 표시</p>
</li>
<li><p><code>기본 생성자 필수</code></p>
<h2 id="📗임베디드-타입-예시">📗임베디드 타입 예시</h2>
<blockquote>
<p>Member 엔티티는 이름, 근무일(근무 시작일, 근무 종료일), 주소(도시, 번지, 우편번호) 필드를 가진다.</p>
</blockquote>
</li>
</ul>
<h3 id="📌임베디드-타입-적용-전">📌임베디드 타입 적용 전</h3>
<h4 id="memberjava">Member.java</h4>
<pre><code class="language-java">@Entity
public class Member{
    @Id @GeneratedValue
    @Column(name=&quot;MEMBER_ID&quot;)
    private Long id;

    @Column(name = &quot;USERNAME&quot;)
    private String username;

    //기간
    private LocalDateTime startDate;
    private LocalDateTime endDate;

    //주소
    private String city;
    private String street;
    private String zipcode;
}</code></pre>
<ul>
<li>기존 코드와 동일하다.</li>
</ul>
<h3 id="📌임베디드-타입-적용-후">📌임베디드 타입 적용 후</h3>
<h4 id="periodjava">Period.java</h4>
<pre><code class="language-java">//값타입 정의
@Embeddable
public class Period {
    private LocalDateTime startDate;
    private LocalDateTime endDate;

    public Period(){}

    public Period(LocalDateTime startDate, LocalDateTime endDate) {
        this.startDate = startDate;
        this.endDate = endDate;
    }
    ...
}</code></pre>
<h4 id="addressjava">Address.java</h4>
<pre><code class="language-java">@Embeddable
public class Address {
    private String city;
    private String street;
    private String zipcode;

    public Address() {

    }

    public Address(String city, String street, String zipcode) {
        this.city = city;
        this.street = street;
        this.zipcode = zipcode;
    }
    ...
}</code></pre>
<ul>
<li>근무시작일(startDate)과 근무종료일(endDate)를 필드로 가지는 <code>Period 클래스</code>를 생성</li>
<li>도시(city), 번지(street), 우편번호(zipcode)를 필드로 가지는 <code>Address 클래스</code>를 생성</li>
<li><code>@Embeddable</code>을 사용하여 임베디드 타입임을 정의</li>
</ul>
<h4 id="memberjava-1">Member.java</h4>
<pre><code class="language-java">@Entity
public class Member{
    @Id @GeneratedValue
    @Column(name=&quot;MEMBER_ID&quot;)
    private Long id;

    @Column(name = &quot;USERNAME&quot;)
    private String username;

    //기간
    @Embedded
    private Period workPeriod;

    //주소
    @Embedded
    private Address homeAddress;
    ...
}</code></pre>
<ul>
<li>기존에 필드로 직접 작성했던 근무일, 주소의 필드들을 지우고 <code>@Embedded</code>를 사용하여 Period, Address 클래스를 이용하여 필드를 만들었다.</li>
</ul>
<h4 id="member-값-저장">Member 값 저장</h4>
<pre><code class="language-java">Member member = new Member();
member.setUsername(&quot;hello&quot;);
member.setHomeAddress(new Address(&quot;city&quot;,&quot;street&quot;,&quot;1000&quot;));
member.setWorkPeriod(new Period());

em.persist(member);</code></pre>
<ul>
<li>임베디드 타입을 사용함으로써 아래와 같은 엔티티 구조로 변경됐다.<img src="https://velog.velcdn.com/images/dev_ssj/post/d9de65c4-83b6-4a67-adbd-a36552e6604c/image.png" />


</li>
</ul>
<h2 id="📗임베디드-타입의-장점">📗임베디드 타입의 장점</h2>
<ul>
<li>재사용이 가능하다.<ul>
<li>Period, Address 는 Member외에 다른 곳에서도 사용 가능</li>
</ul>
</li>
<li>높은 응집도를 가진다</li>
<li>Period.isWork()처럼 해당 값 타입만 사용하는 의미 있는 메서드를 만들 수 있다.</li>
<li>임베디드 타입을 포함한 모든 값 타입은 값 타입을 소유한 엔티티에 생명주기를 의존한다.<ul>
<li>엔티티가 생성될 때 임베디드 타입에도 값이 들어오고, 엔티티 사용이 끝났을 때 값 타입도 함께 사용이 끝난다.</li>
</ul>
</li>
</ul>
<h2 id="📗임베디드-타입과-테이블-매핑">📗임베디드 타입과 테이블 매핑</h2>
<ul>
<li>임베디드 타입은 엔티티의 값일 뿐이다.</li>
<li>임베디드 타입을 사용하기 전과 후에 매핑하는 테이블에는 변화가 없다.</li>
<li>객체와 테이블을 아주 세밀하게 매핑하는 것이 가능</li>
<li>잘 설계한 ORM 애플리케이션은 매핑한 테이블의 수보다 클래스의 수가 더 많다.<img src="https://velog.velcdn.com/images/dev_ssj/post/820b3fd6-d6cc-4b0e-9278-38ea624fb24c/image.png" />

</li>
</ul>
<h2 id="📗하나의-엔티티에-같은-임베디드-타입을-사용하고-싶다면-attributeoverride">📗하나의 엔티티에 같은 임베디드 타입을 사용하고 싶다면? @AttributeOverride</h2>
<h3 id="📌attributeoverride-적용-전">📌@AttributeOverride 적용 전</h3>
<pre><code class="language-java">@Entity
public class Member{
    @Id @GeneratedValue
    @Column(name=&quot;MEMBER_ID&quot;)
    private Long id;

    @Column(name = &quot;USERNAME&quot;)
    private String username;

    //기간
    @Embedded
    private Period workPeriod;

    //주소
    @Embedded
    private Address homeAddress;

    @Embedded
    private Address oldAddress;    //에러! repeated column in mapping for entity.
    ...
}</code></pre>
<ul>
<li>하나에 엔티티에서 @Embedded만을 이용하여 같은 임베디드 타입을 사용하려하면 에러가 발생한다.</li>
</ul>
<h3 id="📌attributeoverride-적용-후">📌@AttributeOverride 적용 후</h3>
<h4 id="memberjava-2">Member.java</h4>
<pre><code class="language-java">@Embedded
private Address homeAddress;

@Embedded
@AttributeOverrides({
         @AttributeOverride(name=&quot;city&quot;,
                 column=@Column(name = &quot;work_city&quot;)),
         @AttributeOverride(name=&quot;street&quot;,
                 column=@Column(name = &quot;work_street&quot;)),
         @AttributeOverride(name=&quot;zipcode&quot;,
                 column=@Column(name = &quot;work_zipcode&quot;))
})</code></pre>