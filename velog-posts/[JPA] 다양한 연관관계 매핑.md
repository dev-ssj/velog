<h1 id="💡-연관관계-매핑-시-고려해야할-사항-3가지">💡 연관관계 매핑 시 고려해야할 사항 3가지</h1>
<h3 id="다중성">다중성</h3>
<ul>
<li>다대일(@ManyToOne)</li>
<li>일대다(@OneToMany)</li>
<li>일대일(@OneToOne)</li>
<li>다대다(@ManyToMany)</li>
</ul>
<h3 id="단방향-양방향">단방향, 양방향</h3>
<ul>
<li>테이블 : 외래키 하나로 양쪽 조인 가능. 방향이라는 개념이 없다</li>
<li>객체 : 참조용 필드가 있는 쪽으로만 참조가능. 한쪽만 참조하면 단방향, 양쪽이 서로 참조하면 양방향<h3 id="연관관계의-주인">연관관계의 주인</h3>
<ul>
<li>외래키를 관리하는 곳이 연관관계의 주인</li>
<li>주인의 반대편은 외래키에 영향을 주지않음. 단순 조회만 가능</li>
</ul>
</li>
</ul>
<h1 id="💡다중성">💡다중성</h1>
<h2 id="📗다대일n1">📗다대일[N:1]</h2>
<h3 id="1-다대일-단방향">1. 다대일 단방향</h3>
 <img src="https://velog.velcdn.com/images/dev_ssj/post/3ed2a544-ac98-4c60-89de-057bb7eb3e33/image.png" />

<ul>
<li><p>가장 많이 사용하는 연관관계</p>
</li>
<li><p>다대일의 반대는 일대다</p>
<h3 id="2-다대일-양방향">2. 다대일 양방향</h3>
<img src="https://velog.velcdn.com/images/dev_ssj/post/2e1e348a-6811-4f38-aa45-0ede704d071c/image.png" />
</li>
<li><p>외래 키가 있는 쪽이 연관관계의 주인</p>
</li>
<li><p>양쪽을 서로 참조하도록 개발</p>
<h2 id="📗일대다1n">📗일대다[1:N]</h2>
<blockquote>
<p><strong>권장하지 않는 모델이므로 사용을 지양해야한다</strong></p>
</blockquote>
<h3 id="1-일대다-단방향">1. 일대다 단방향</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/46d1e187-daaa-4ee8-aa03-d894bf3800ce/image.png" /></p>
<ul>
<li>일대다(1:N)에서 일(1)이 연관관계의 주인</li>
<li>객체와 테이블의 차이떄문에 반대편 테이블의 외래 키를 관리하는 특이한 구조</li>
<li>단점</li>
<li>엔티티가 관리하는 외래키가 다른 테이블에 있음. </li>
<li>연관관계 관리를 위해 추가로 UPDATE SQL 실행</li>
</ul>
</li>
</ul>
<p>➡️일대다 단방향 매핑보다는 <strong>다대일 양방향 매핑</strong>을 사용하자. </p>
<h3 id="2-일대다-양방향">2. 일대다 양방향</h3>
<img src="https://velog.velcdn.com/images/dev_ssj/post/3790976e-6766-4e16-b295-cdcf03c2171f/image.png" />

<ul>
<li>이런 매핑은 공식적으로 존재하지 않는다.</li>
<li>읽기 전용 필드를 사용해서 양방향처럼 사용하는 방법</li>
<li><strong>다대일 양방향을 사용하자</strong></li>
</ul>
<h2 id="📗일대일11">📗일대일[1:1]</h2>
<blockquote>
<ul>
<li>일대일 관계는 그 반대도 일대일</li>
</ul>
</blockquote>
<ul>
<li>주 테이블이나 대상 테이블 중에 외래 키 선택 가능<ul>
<li>주 테이블에 외래 키</li>
<li>대상 테이블에 외래 키</li>
</ul>
</li>
<li>외래 키 데이터베이스 유니크 제약조건 추가</li>
</ul>
<h3 id="1-일대일--주-테이블에-외래키-단방향">1. 일대일 : 주 테이블에 외래키 단방향</h3>
<img src="https://velog.velcdn.com/images/dev_ssj/post/061486ec-706f-435e-aa93-51db229d2f17/image.png" />

<ul>
<li>다대일 단방향 매핑과 유사</li>
</ul>
<h3 id="2-일대일--주-테이블에-외래키-양방향">2. 일대일 : 주 테이블에 외래키 양방향</h3>
<img src="https://velog.velcdn.com/images/dev_ssj/post/92da4288-1e28-4945-a5a8-e8de04bd2ac8/image.png" />

<ul>
<li>다대일 양방향 매핑처럼 <code>외래키가 있는 곳이 연관관계의 주인</code></li>
<li>연관관계의 주인 반대편은 <code>mappedBy</code> 적용</li>
</ul>
<h3 id="3-일대일--대상-테이블에-외래키-단방향">3. 일대일 : 대상 테이블에 외래키 단방향</h3>
<img src="https://velog.velcdn.com/images/dev_ssj/post/a580e2ef-c677-4724-8aed-fb22a5ad4640/image.png" />


<ul>
<li>단방향 관계는 <code>JPA 지원X</code></li>
</ul>
<h3 id="4-일대일--대상-테이블에-외래키-양방향">4. 일대일 : 대상 테이블에 외래키 양방향</h3>
<img src="https://velog.velcdn.com/images/dev_ssj/post/00af8f9c-78b9-4fcd-ba02-65e179b7e631/image.png" />

<ul>
<li>일대일 주 테이블에 외래키 양방향과 매핑 방법은 같다.</li>
</ul>
<h3 id="일대일-정리">일대일 정리</h3>
<blockquote>
<p><strong>📌주테이블에 외래 키</strong></p>
</blockquote>
<ul>
<li><p>주 객체가 대상 객체의 참조를 가지는 것 처럼 주 테이블에 외래 키를 두고 대상 테이블을 찾음</p>
</li>
<li><p>객체지향 개발자 선호</p>
</li>
<li><p>JPA 매핑 편리</p>
</li>
<li><p>장점 : 주 테이블만 조회해도 대상 테이블에 데이터가 있는지 확인 가능</p>
</li>
<li><p>단점 : 값이 없으면 외래 키에 null 허용</p>
<blockquote>
<p><strong>📌대상 테이블에 외래 키</strong></p>
</blockquote>
<ul>
<li>대상 테이블에 외래 키가 존재</li>
<li>전통적인 데이터베이스 개발자 선호</li>
<li>장점 : 주 테이블과 대상 테이블을 일대일에서 일대다 관계로 변경할 때 테이블 구조 유지</li>
<li>단점 : 프록시 기능의 한계로 지연로딩으로 설정해도 항상 즉시 로딩됨</li>
</ul>
</li>
</ul>
<h2 id="📗다대다nm">📗다대다[N:M]</h2>
<blockquote>
<p><strong>관계형 데이터베이스는 정규화된 테이블 2개로 다대다 관계를 표현할 수 없음</strong>
연결 테이블을 추가해서 일대다, 다대일 관계로 풀어내야 함</p>
</blockquote>
<img src="https://velog.velcdn.com/images/dev_ssj/post/ae612236-880f-4798-a5a8-4dad2479cf11/image.png" />

<img src="https://velog.velcdn.com/images/dev_ssj/post/eff95bf0-d1e4-4786-a1aa-190ea46da208/image.png" />

<ul>
<li>객체는 컬렉션을 사용해서 객체 2개로 다대다 관계 가능</li>
<li>@ManyToMany 사용</li>
<li>@JoinTable로 연결 테이블 지정</li>
<li>단방향, 양방향 가능</li>
</ul>
<h3 id="다대다-매핑의-한계">다대다 매핑의 한계</h3>
<ul>
<li><strong>편리해보이지만 실무에서 사용 불가</strong></li>
<li>연결 테이블이 단순히 연결만 하고 끝나지 않고, 주문시간, 수량 같은 데이터가 들어올 수 있음<img src="https://velog.velcdn.com/images/dev_ssj/post/53ce7d54-c7eb-4666-aef2-cf7232ab19a1/image.png" />

</li>
</ul>
<h3 id="다대다-매핑의-한계-극복">다대다 매핑의 한계 극복</h3>
<ul>
<li><strong>연결 테이블용 엔티티 추가(연결 테이블을 엔티티로 승격)</strong></li>
<li><strong>@ManyToMany -&gt; @OneToMany, @ManyToOne</strong><img src="https://velog.velcdn.com/images/dev_ssj/post/7125c002-5745-48bf-ad21-7fe9dd1e67f1/image.png" />
</li>
</ul>