<h1 id="💡객체와-테이블-매핑">💡객체와 테이블 매핑</h1>
<blockquote>
<ul>
<li>객체와 테이블 매핑 : <code>@Entity</code>, <code>@Table</code></li>
</ul>
</blockquote>
<ul>
<li>필드와 컬럼 매핑 : <code>@Column</code></li>
<li>기본 키 매핑 : <code>@Id</code></li>
<li>연관관계 매핑 : <code>@ManyToOne</code>,<code>@JoinColumn</code> </li>
</ul>
<h2 id="📗entity">📗@Entity</h2>
<ul>
<li><code>@Entity</code>가 붙은 클래스는 JPA가 관리하며, <code>엔티티</code>라고한다.</li>
<li>JPA를 사용해서 테이블과 매핑할 클래스는 <code>@Entity</code> 필수!\</li>
</ul>
<blockquote>
<p>📌 <strong>주의</strong></p>
</blockquote>
<ul>
<li><code>기본 생성자</code> 필수(파라미터가 없는 public 또는 protected 생성자)</li>
<li>final 클래스, enum, interface, inner 클래스에 사용 불가</li>
<li>저장할 필드에 <code>final 사용 불가</code></li>
</ul>
<h3 id="entity-속성">@Entity 속성</h3>
<ul>
<li><strong>name</strong><ul>
<li>JPA에서 사용할 <code>엔티티 이름</code>을 지정한다.</li>
<li>기본값 : <code>클래스 이름</code>을 그대로 사용</li>
<li>같은 클래스명이 없으면 <code>가급적 기본값</code>을 사용한다.</li>
</ul>
</li>
</ul>
<h2 id="📗table">📗@Table</h2>
<ul>
<li><code>@Table</code>은 <code>엔티티와 매핑할 테이블</code>을 지정한다.</li>
</ul>
<h3 id="table-속성">@Table 속성</h3>
<table>
<thead>
<tr>
<th>속성</th>
<th>기능</th>
<th>기본값</th>
</tr>
</thead>
<tbody><tr>
<td><strong>name</strong></td>
<td>매핑할 테이블 명</td>
<td>엔티티 이름을 사용</td>
</tr>
<tr>
<td><strong>catalog</strong></td>
<td>데이터베이스 catalog 매핑</td>
<td></td>
</tr>
<tr>
<td><strong>schema</strong></td>
<td>데이터베이스 schema 매핑</td>
<td></td>
</tr>
<tr>
<td><strong>uniqueConstraints(DDL)</strong></td>
<td>DDL 생성시 유니크 제약 조건 생성</td>
<td></td>
</tr>
</tbody></table>
<h2 id="📗테이블-생성-예시">📗테이블 생성 예시</h2>
<pre><code class="language-java">@Entity
@Table(name = &quot;Members&quot;)    //DB의 Members테이블과 매핑
public class Member{
    @Id 
    private Long id;
    private String name;
}</code></pre>
<br />

<h1 id="💡데이터베이스-스키마-자동-생성">💡데이터베이스 스키마 자동 생성</h1>
<blockquote>
<ul>
<li>DDL을 애플리케이션 <code>실행 시점에 자동 생성</code></li>
</ul>
</blockquote>
<ul>
<li>테이블 중심 -&gt; <code>객체 중심</code></li>
<li>데이터베이스 방언을 활용해서 <code>데이터베이스에 맞는 적절한 DDL</code> 생성</li>
<li>이렇게 생성된 DDL은 <code>개발 장비에서만</code> 사용</li>
<li>생성된 DDL은 운영서버에서는 사용하지 않거나, 적절히 다듬은 후 사용</li>
</ul>
<h3 id="속성">속성</h3>
<table>
<thead>
<tr>
<th>옵션</th>
<th>속성</th>
</tr>
</thead>
<tbody><tr>
<td><strong>create</strong></td>
<td>기존 테이블 삭제 후 다시 생성 (DROP + CREATE)</td>
</tr>
<tr>
<td><strong>create-drop</strong></td>
<td>create와 같으나 종료시점에 테이블 DROP</td>
</tr>
<tr>
<td><strong>update</strong></td>
<td>변경된 내역만 반영(운영 DB에는 사용하면 안됨)</td>
</tr>
<tr>
<td><strong>validate</strong></td>
<td>엔티티와 테이블이 정상 매핑되었는지만 확인</td>
</tr>
<tr>
<td><strong>none</strong></td>
<td>사용하지 않음</td>
</tr>
</tbody></table>
<h2 id="📗데이터베이스-스키마-자동생성-예시">📗데이터베이스 스키마 자동생성 예시</h2>
<h4 id="persistencexml">persistence.xml</h4>
<pre><code class="language-xml">&lt;persistence-unit name=&quot;hello&quot;&gt;
        &lt;properties&gt;
            &lt;!-- 필수 속성 --&gt;
            ...

            &lt;!-- 옵션 --&gt;
            &lt;property name=&quot;hibernate.show_sql&quot; value=&quot;true&quot;/&gt;
            &lt;property name=&quot;hibernate.format_sql&quot; value=&quot;true&quot;/&gt;
              &lt;!--DB방언 설정--&gt;
            &lt;property name=&quot;hibernate.use_sql_comments&quot;  value=&quot;true&quot;/&gt;
              &lt;!--데이터베이스 스키마 자동생성 속성--&gt;
            &lt;property name=&quot;hibernate.hbm2ddl.auto&quot; value=&quot;create&quot; /&gt;    
        &lt;/properties&gt;
    &lt;/persistence-unit&gt;</code></pre>
<h2 id="📗주의점">📗주의점</h2>
<ul>
<li><span style="color: red;"> <strong>운영장비에는 절대 create, create-drop, update를 사용하면 안된다</strong></span></li>
<li><strong>개발 초기 단계</strong>는 <code>create</code> 또는 <code>update</code></li>
<li><strong>테스트 서버</strong>는 <code>update</code> 또는 <code>validate</code></li>
<li><strong>스테이징과</strong> 운영 서버는 <code>validate</code> 또는 <code>none</code></li>
</ul>
<br />

<h1 id="💡필드와-컬럼-매핑">💡필드와 컬럼 매핑</h1>
<blockquote>
<ul>
<li>필드와 컬럼 매핑 어노테이션을 알아보자</li>
</ul>
</blockquote>
<h2 id="📗매핑-어노테이션-정리">📗매핑 어노테이션 정리</h2>
<table>
<thead>
<tr>
<th>어노테이션</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><strong>@Column</strong></td>
<td>컬럼 매핑</td>
</tr>
<tr>
<td><strong>@Temporal</strong></td>
<td>날짜 타입 매핑</td>
</tr>
<tr>
<td><strong>@Enumerated</strong></td>
<td>enum 타입 매핑</td>
</tr>
<tr>
<td><strong>@Lob</strong></td>
<td>BLOB, CLOB 매핑</td>
</tr>
<tr>
<td><strong>@Transient</strong></td>
<td>특정 필드를 컬럼에 매핑하지 않음(매핑 무시)</td>
</tr>
</tbody></table>
<h2 id="📗column">📗@Column</h2>
<h3 id="1name">(1)name</h3>
<blockquote>
<ul>
<li>필드와 매핑할 <code>테이블의 컬럼명</code> 설정</li>
</ul>
</blockquote>
<ul>
<li>기본 값은 객체의 <code>필드명</code>이다.<pre><code class="language-java">//DB의 컬럼명이 user_name인 컬럼과 매핑
@Colum(name =&quot;user_name&quot;)
private String name;</code></pre>
</li>
</ul>
<h3 id="2insertable-updatable">(2)insertable, updatable</h3>
<blockquote>
<ul>
<li>컬럼을 등록 또는 수정 했을 때 DB에 변경 할지 여부 설정</li>
</ul>
</blockquote>
<ul>
<li>기본 값은 <code>TRUE</code><pre><code class="language-java">//등록은 OK. 변경은 하면 안될때
@Colum(updatable = false)
private String name;</code></pre>
</li>
</ul>
<h3 id="3nullable">(3)nullable</h3>
<blockquote>
<ul>
<li>컬럼의 <code>null</code>값 허용 여부 설정</li>
</ul>
</blockquote>
<ul>
<li><p>기본 값은 <code>TRUE</code></p>
<pre><code class="language-java">//not null로 설정
@Colum(nullable = false)
private String name;</code></pre>
<h3 id="4unique">(4)unique</h3>
<blockquote>
<ul>
<li>컬럼에 간단한 <code>유니크 제약 조건</code>을 건다</li>
</ul>
</blockquote>
</li>
<li><p><code>@Table</code>의 <code>uniqueConstraints</code>와 같지만, <code>uniqueConstraints</code>는 유니크 제약조건의 이름 설정이 가능하므로 잘 사용하지 않는다.</p>
<pre><code class="language-java">//유니크 제약조건 설정
@Colum(unique = true)
private String name;</code></pre>
<img src="https://velog.velcdn.com/images/dev_ssj/post/35ac90db-9fb6-4f96-9366-ba138b98d981/image.png" /></li>
<li><p>유니크 제약조건 명이 랜덤으로 들어간다.</p>
<pre><code class="language-java">@Entity
//@Table의 uniqueConstraints를 사용하면 유니크 제약조건 이름을 설정할 수 있다.
@Table(uniqueConstraints=&quot;Member_nuiqe&quot;)    
public class Member{
 ...
}</code></pre>
</li>
</ul>
<h3 id="5columndefinition">(5)columnDefinition</h3>
<blockquote>
<ul>
<li>데이터베이스의 <code>컬럼 정보</code>를 직접 설정</li>
</ul>
</blockquote>
<pre><code class="language-java"> //컬럼 타입은 varchar(100), 디폴트값은 'EMPTY'
@Colum(columnDefinition=&quot;varchar(100) default 'EMPTY')
private String name;</code></pre>
<h3 id="6length">(6)length</h3>
<blockquote>
<ul>
<li><code>문자 길이</code> 제약 조건</li>
</ul>
</blockquote>
<ul>
<li><p><code>String</code> 타입에만 사용 가능</p>
</li>
<li><p>기본 값은 <code>255</code></p>
<pre><code class="language-java">//컬럼타입 길이를 10으로 변경 - varchar(10)
@Colum(lenght = 10)
private String name;</code></pre>
<h3 id="7percisionscale">(7)percision,scale</h3>
<blockquote>
<ul>
<li><code>아주 큰 숫자</code>나 <code>정밀한 소수</code>를 다루어야 할 때만 사용</li>
</ul>
</blockquote>
</li>
<li><p><code>percision</code>은 소수점을 포함한 전체 자릿수를, <code>scale</code>은 소수의 자릿수를 의미</p>
<ul>
<li><code>BigDecimal</code> 타입에서 사용</li>
</ul>
</li>
</ul>
<h2 id="📗enumerated">📗@Enumerated</h2>
<blockquote>
<ul>
<li>자바 <code>enum 타입</code>을 매핑할 때 사용</li>
</ul>
</blockquote>
<ul>
<li>기본 값은 <code>EnumType.ORDINAL</code></li>
<li><code>EnumType.ORDINAL</code> : <code>enum 순서</code>를 데이터베이스에 저장</li>
<li><code>EnumType.STRING</code> :  <code>enum 이름</code>을 데이터베이스에 저장<pre><code class="language-java">@Enumerated(EnumType.STRING)
private int age;</code></pre>
</li>
</ul>
<blockquote>
<p>📌 <strong><span style="color: red;">주의! ORDINAL는 사용 하면 안된다!<span style="color: red;"></strong></p>
</blockquote>
<ul>
<li>ORDINAL는 enum의 순서(0,1...)로 enum을 저장한다.</li>
<li>이미 만들어둔 enum의 순서가 바뀌거나, 변경(추가,삭제)가 된다면?
➡️엄청나게 큰일이 벌어진다...</li>
</ul>
<h2 id="📗temporal">📗@Temporal</h2>
<blockquote>
<ul>
<li>날짜 타입(<code>java.util.Date</code>, <code>java,util,Calendar</code>)을 매핑할 때 사용</li>
</ul>
</blockquote>
<ul>
<li><p><code>LocalDate</code>, <code>LocalDateTime</code>을 사용할 때는 <code>생략 가능</code>(최신 하이버 네이트 지원)</p>
<pre><code class="language-java">//날짜. 데이터베이스 date 타입과 매핑(예 : 2013-10-11)
@Temporal(TemporalType.DATE)
private Date date1;
//시간. 데이터베이스 time 타입과 매핑(예 : 11:11:11)
@Temporal(TemporalType.TIME)
private Date date2;
//날짜와 시간. 데이터베이스 timestamp타입과 매핑(예 : 2013-10-11 11:11:11)
@Temporal(TemporalType.TIMESPAMP)
private Date date3;</code></pre>
<h2 id="📗lob">📗@LOB</h2>
<blockquote>
<ul>
<li>데이터베이스 <code>BLOB</code>, <code>CLOB</code> 타입과 매핑</li>
</ul>
</blockquote>
</li>
<li><p>매핑하는 필드 타입이 문자면 <code>CLOB</code> 매핑, 나머지는 <code>BLOB</code> 매핑</p>
<pre><code class="language-java">@LOB
private String text;</code></pre>
<h2 id="📗transient">📗@Transient</h2>
<blockquote>
<ul>
<li>특정 필드를 <code>컬럼에 매핑하지 않을 때</code> 사용</li>
</ul>
</blockquote>
</li>
<li><p>필드 매핑 X, 데이터베이스에 저장X, 조회X</p>
<ul>
<li>주로 메모리상에서만 임시로 어던 값을 보관하고 싶을 때 사용<pre><code class="language-java">@Transient
private Integer temp;</code></pre>
</li>
</ul>
</li>
</ul>
<h1 id="💡기본-키-매핑">💡기본 키 매핑</h1>
<blockquote>
<p>기본 키 매핑에 대해 알아보자</p>
</blockquote>
<h2 id="📗-기본-키-매핑---직접-할당id">📗 기본 키 매핑 - 직접 할당(@Id)</h2>
<blockquote>
<ul>
<li>@Id 어노테이션을 사용하면 기본키 직접 할당이 된다.</li>
</ul>
</blockquote>
<pre><code class="language-java">  @Id
  private Long id;</code></pre>
<h2 id="📗-기본-키-매핑---자동생성generatedvalue">📗 기본 키 매핑 - 자동생성(@GeneratedValue)</h2>
<ul>
<li><code>IDENTITY</code> : 데이터베이스에 위임. MYSQL</li>
<li><code>SEQUENCE</code> : 데이터베이스 시퀀스 오브젝트 사용. ORACLE<ul>
<li><code>@SequenceGenerator</code> 필요</li>
</ul>
</li>
<li><code>TABLE</code> : 키 생성용 테이블 사용. 모든 DB에서 사용 가능<ul>
<li><code>@TableGenerator</code> 필요</li>
<li><code>AUTO</code> : 방언에 따라 자동 지정. 기본 값</li>
</ul>
</li>
</ul>
<h3 id="1identity-전략">(1)IDENTITY 전략</h3>
<ul>
<li>기본 키 생성을 데이터베이스에 위임</li>
<li>주로 MYSQL, PostagreSQL, SQL Server, DB2에서 사용<ul>
<li>ex) MySQL의 AUTO_INCREMENT</li>
<li>AUTO_INCREMENT는 데이터베이스에 INSERT SQL을 실행한 후에 ID 값을 알 수 있다.
➡️ IDENTITY 전략은 em.persist()시점에 즉시 INSERT SQL 실행 후 DB에서 식별자를 조회<h3 id="identity-전략-예시">IDENTITY 전략 예시</h3>
<pre><code class="language-java">@Id @GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;</code></pre>
</li>
</ul>
</li>
</ul>
<h3 id="2sequence-전략">(2)SEQUENCE 전략</h3>
<ul>
<li>데이터베이스 시퀀스를 사용</li>
<li><code>데이터베이스 시퀀스</code> : 유일한 값을 순서대로 생성하는 특별한 데이터 베이스 오브젝트</li>
<li>오라클, PostagreSQL, DB2, H2 데이터베이스에서 사용</li>
<li>SEQUENCE 전략 사용 시 <code>@SequenceGenerator</code> 필요<h4 id="📌sequencegenerator-속성">📌@SequenceGenerator 속성</h4>
<img src="https://velog.velcdn.com/images/dev_ssj/post/ec2f598f-c2b1-4fdd-a30a-c6d203370f94/image.png" />



</li>
</ul>
<h3 id="sequence-전략-예시">SEQUENCE 전략 예시</h3>
<pre><code class="language-java">@Entity
@SequenceGenerator(name=&quot;MEMBER_SEQ_GENERATOR&quot;),
                      sequenceName=&quot;MEMBER_SEQ&quot;,
                      initialValue=1, allocationSize=1)
public class Member{

    @Id @GeneratedValue(strategy = GenerationType.SEQUENCE,
                          generator=&quot;MEMBER_SEQ_GENERATOR&quot;)
    private Long id;
}</code></pre>
<h3 id="3table-전략">(3)TABLE 전략</h3>
<ul>
<li><code>키 생성 전용 테이블</code>을 하나 만들어서 데이터베이스 시퀀스를 흉내내는 전략</li>
<li>TABLE 전략 사용 시 <code>@TableGenerator</code> 필요</li>
<li><code>장점</code> : 모든 데이터베이스에 적용 가능</li>
<li><code>단점</code> : 성능</li>
</ul>
<h4 id="📌tablegenerator-속성">📌@TableGenerator 속성</h4>
  <img src="https://velog.velcdn.com/images/dev_ssj/post/7f82b797-df59-46a0-bb28-a1d2b20cb460/image.png" />

<h2 id="📗권장하는-식별자-전략">📗권장하는 식별자 전략</h2>
<ul>
<li><code>기본 키 제약 조건</code> : null 아님, 유일, 변하면 안된다</li>
<li>미래까지 이 조건을 만족하는 자연키는 찾기 어렵다.
➡️ <code>대리키(대체키)</code>를 사용하자!</li>
<li><strong>권장 : Long형 + 대체키 + 키 생성전략 사용</strong></li>
</ul>