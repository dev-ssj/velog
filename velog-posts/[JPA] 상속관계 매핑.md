<h1 id="💡상속관계-매핑">💡상속관계 매핑</h1>
<ul>
<li>관계형 데이터베이스는 상속 관계가 없다.</li>
<li>슈퍼타입 서브타입 관계라는 모델링 기법이 객체 상속과 유사</li>
<li><code>상속관계 매핑</code> : 객체의 상속 구조와 DB의 슈퍼타입 서브타입 관계를 매핑<img src="https://velog.velcdn.com/images/dev_ssj/post/57f5c449-7bc1-416b-a377-a9e9c6ff2bbc/image.png" />


</li>
</ul>
<blockquote>
<p>📌<strong>슈퍼타입 서브타입 논리 모델을 실제 물리 모델로 구현하는 방법</strong></p>
</blockquote>
<ul>
<li>각각 테이블로 변환 : <code>조인 전략</code></li>
<li>통합 테이블로 변환 : <code>단일 테이블 전략</code></li>
<li>서브타입 테이블로 변환 -&gt; <code>구현 클래스마다 테이블 전략</code></li>
</ul>
<h2 id="📗주요-어노테이션">📗주요 어노테이션</h2>
<ul>
<li><code>@inheritance(strategy = inheritanceType.XXX)</code><ul>
<li><code>JOINED</code> : 조인전략</li>
<li><code>SINGLE_TABLE</code> : 단일 테이블 전략</li>
<li><code>TABLE_PER_CLASS</code> : 구현 클래스마다 테이블 전략</li>
</ul>
</li>
<li><code>DiscriminatorColumn(name=&quot;DTYPE&quot;)</code><ul>
<li><code>부모 클래스</code>에 구분 컬럼을 지정한다.</li>
<li>이 컬럼으로 저장된 자식테이블을 구분할 수 있다. </li>
<li>기본 값 : DTYPE</li>
</ul>
</li>
<li><code>DiscriminatorValue(&quot;XXX&quot;)</code><ul>
<li><code>하위 클래스에 선언</code></li>
<li>엔티티 저장 시, 구분 컬럼에 입력할 값을 지정한다.</li>
<li>어노테이션을 선언하지 않을 경우 기본값으로 <code>클래스 이름</code>이 들어간다.</li>
</ul>
</li>
</ul>
<h2 id="📗조인-전략">📗조인 전략</h2>
<img src="https://velog.velcdn.com/images/dev_ssj/post/9942a021-c618-46cc-8d2a-4954d646113f/image.png" />

<ul>
<li><p>특징</p>
<ul>
<li>부모, 자식 엔티티를 각각 <code>모두 테이블로 만들어 조인</code>해서 조회</li>
<li>자식 객체는 Default DType 컬럼으로 구분된다.</li>
<li><code>@Inheritance(strategy = InheritanceType.JOINED)</code></li>
</ul>
</li>
<li><p>장점 </p>
<ul>
<li>테이블의 정규화</li>
<li><code>외래 키 참조 무결성 제약조건</code> 활용 가능</li>
<li>저장공간 효율화</li>
</ul>
</li>
<li><p>단점</p>
<ul>
<li><p>조회 시 조인을 많이 사용 -&gt; 성능 저하</p>
</li>
<li><p>조회 쿼리가 복잡함</p>
</li>
<li><p>데이터 저장시 INSERT SQL 2번 호출</p>
<h3 id="📌조인-전략-예시">📌조인 전략 예시</h3>
<h4 id="item엔티티">Item엔티티</h4>
<pre><code class="language-java">@Entity
//조인 전략으로 지정
@Inheritance(strategy=InheritanceType.JOINED)
@DiscriminatorColumn    //DTYPE 생성
public class Item{
@Id @GeneratedValue
private Long id;

private String name;
private int price;
}</code></pre>
<h4 id="album엔티티">Album엔티티</h4>
<pre><code class="language-java">@Entity
@DiscriminatorValue(&quot;A&quot;)    //DTYPE명을 A로 지정
public class Album extends Item{
private String artist;
}</code></pre>
<h4 id="movie엔티티">Movie엔티티</h4>
<pre><code class="language-java">@Entity
@DiscriminatorValue(&quot;M&quot;)    //DTYPE명을 B로 지정
public class Movie extends Item{
private STring director;
private STring actor;
}</code></pre>
<h4 id="실행-결과">실행 결과</h4>
<img src="https://velog.velcdn.com/images/dev_ssj/post/b47e87f6-421f-4c53-8d32-86197debd3bc/image.png" />
<img src="https://velog.velcdn.com/images/dev_ssj/post/6c6174de-243c-47f4-8317-e18a2b17bd7b/image.png" />
<br />

</li>
</ul>
</li>
</ul>
<h2 id="📗단일-테이블-전략">📗단일 테이블 전략</h2>
<img src="https://velog.velcdn.com/images/dev_ssj/post/5cf62e75-d1af-4b3c-b335-339be953aa31/image.png" />

<ul>
<li>특징<ul>
<li>모든 자식 엔티티가 부모 엔티티와 함께 <code>단 하나의 테이블</code>에 저장되는 전략</li>
<li><code>@Inheritance(strategy=InheratanceType.SINGLE_TABLE)</code></li>
<li>장점</li>
<li>조인이 필요 없으므로 일반적으로 <code>조회 성능이 빠름</code></li>
<li>조회 쿼리가 단순</li>
<li>단점</li>
<li>자식 엔티티가 매핑한 컬럼을 모두 null 허용</li>
<li>단일 테이블에 모든 것을 저장하므로 테이블이 커질 수 있다.</li>
<li>상황에 따라 조회 성능이 오히려 느려질 수 있다.<h3 id="📌단일-테이블-전략-예시">📌단일 테이블 전략 예시</h3>
<h4 id="item엔티티-1">Item엔티티</h4>
<pre><code class="language-java">@Entity
//단일 테이블 전략으로 지정
@Inheritance(strategy=InheritanceType.SINGLE_TABLE)
@DiscriminatorColum
public class Item{
...
}</code></pre>
<h4 id="실행-결과-1">실행 결과</h4>
<img src="https://velog.velcdn.com/images/dev_ssj/post/8f567f76-473b-4c51-aaa5-ef68a0b06aac/image.png" />
<img src="https://velog.velcdn.com/images/dev_ssj/post/0798cc9f-a1e5-47ea-a2be-fcc399c70443/image.png" />
<br />

</li>
</ul>
</li>
</ul>
<h2 id="📗구현-클래스마다-테이블-전략">📗구현 클래스마다 테이블 전략</h2>
<blockquote>
<p><span style="color: red;">** 이 전략은 데이터베이스 설계자와 ORM 전문가 둘다 추천하지 않는다**</span></p>
</blockquote>
<img src="https://velog.velcdn.com/images/dev_ssj/post/3ce046c6-33a0-4f27-8735-ca0c1fa72c11/image.png" />

<ul>
<li>특징<ul>
<li><code>@Inheritance(strategy=InheritanceType.TABLE_PER_CLASS)</code></li>
</ul>
</li>
<li>장점<ul>
<li>서브 타입을 명확하게 구분해서 처리할 때 효과적</li>
<li>not null 제약조건 사용 가능</li>
</ul>
</li>
<li>단점<ul>
<li>여러 자식 테이블을 함께 조회할 때 성능이 느림(UNION SQL 필요)</li>
<li>자식 테이블을 통합해서 쿼리하기 어려움</li>
</ul>
</li>
</ul>
<h3 id="📌구현-클래스마다-테이블-전략-예시">📌구현 클래스마다 테이블 전략 예시</h3>
<h4 id="item엔티티-2">Item엔티티</h4>
<pre><code class="language-java">@Entity
//구현 클래스마다 테이블 전략 예시
@Inheritance(strategy=InheritanceType.TABLE_PER_CLASS)
//추상클래스로 생성
public abstract class Item{
    ...
}</code></pre>
<h4 id="실행-결과-2">실행 결과</h4>
<img src="https://velog.velcdn.com/images/dev_ssj/post/b796aaf7-bae1-4661-ad2d-9cb7c0f0e0c8/image.png" />