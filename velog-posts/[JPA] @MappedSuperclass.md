<h1 id="💡mappedsuperclass">💡@MappedSuperclass</h1>
<blockquote>
<ul>
<li>엔티티 사이에 <code>공통 매칭 정보</code>가 필요할 때 사용한다(예시에선 id, name)<img src="https://velog.velcdn.com/images/dev_ssj/post/13d9efc7-2a0c-4256-a1d1-62ae482778cb/image.png" />
</li>
</ul>
</blockquote>
<h2 id="📗mappedsuperclass-예시">📗@MappedSuperclass 예시</h2>
<blockquote>
<p>** 📌 요구사항 ** </p>
</blockquote>
<ul>
<li>누가 언제 생성했는지(createdBy, createdDate)</li>
<li>누가 언제 마지막으로 수정했는지 (lastModifiedBy, lastModifiedDate) 
에 관한 정보를 <code>Member</code>, <code>Team</code> 엔티티에 넣고 싶다.</li>
</ul>
<h3 id="baseentity-클래스">BaseEntity 클래스</h3>
<pre><code class="language-java">@MappedSuperclass
 public abstract class BaseEntity{
     private String createdBy;
    private LocalDateTime createdDate;
    private String lastmodifiedBy;
    private LocalDateTiME lastmodifiedDate;
    //getter, setter 생략
 }</code></pre>
<h3 id="team-엔티티">team 엔티티</h3>
<pre><code class="language-java">public class Team extends BaseEntity{
    ...
}</code></pre>
<h3 id="member-엔티티">Member 엔티티</h3>
<pre><code class="language-java">public class Member extends BaseEntity{
    ...
}</code></pre>
<h3 id="실행-결과">실행 결과</h3>
<img src="https://velog.velcdn.com/images/dev_ssj/post/597eaf41-7dee-47f1-9819-7aae61024823/image.png" />

<ul>
<li>BaseEntity의 필드들이 team, member 엔티티에 들어가있다.<br />

</li>
</ul>
<h2 id="📗mappedsuperclass-특징">📗@MappedSuperclass 특징</h2>
<ul>
<li>상속 관계 매핑 X</li>
<li>엔티티 X, 테이블과 매핑X</li>
<li>부모 클래스를 상속 받는 <code>자식 클래스에 매핑 정보만 제공</code>한다.</li>
<li>조회, 검색 불가<ul>
<li><code>em.find(BaseEntity) 불가</code></li>
</ul>
</li>
<li>직접 생성해서 사용할 일이 없으므로 <code>추상 클래스 권장</code>  </li>
<li>테이블과 관계 없고, 단순히 엔티티가 공통으로 사용하는 매핑 정보를 모으는 역할</li>
<li>주로 등록일, 수정일, 등록자, 수정자 같은 전체 엔티티에서 공통으로 적용하는 정보를 모을 때 사용<blockquote>
<p><strong>📌참고</strong>
@Entity 클래스는 엔티티나 @MappedSuperclass로 지정한 클래스만 상속 가능</p>
</blockquote>
</li>
</ul>