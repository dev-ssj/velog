<h2 id="💡인덱스index란">💡인덱스(index)란?</h2>
<blockquote>
<p><code>특정 컬럼</code>의 값을 빠르게 찾기 위해 별도로 유지되는 자료구조를 뜻한다.
책에서 우리가 원하는 내용을 찾아볼 때, 책의 모든 페이지를 하나하나 찾아보는 것은 오랜 시간이 걸린다. 그렇기 때문에 책의 맨 앞이나 맨 뒤에 색인을 추가하는데, 데이터베이스의 index는 <code>색인</code>과 같다.
➡️즉, <strong>데이터 = 책의 내용, 인덱스 = 책의 목차, 물리적 주소 = 책의 페이지 번호</strong> 라고 생각하면 된다.</p>
</blockquote>
<hr />
<h2 id="📗인덱스의-종류">📗인덱스의 종류</h2>
<h3 id="✅-1-b-tree-인덱스balanced-treed-index">✅ 1. B-Tree 인덱스(Balanced Treed Index)</h3>
<h4 id="📍-b-tree-인덱스란">📍 B-Tree 인덱스란?</h4>
<ul>
<li>대부분의 RDBMS 등에서 <strong>기본 인덱스 타입</strong>으로 사용된다</li>
<li>균형 이진 트리(Balannced Tree)를 확장한 <strong>다진 트리(M-ary Tree)</strong> 구조</li>
<li>데이터가 정렬된 상태로 저장되며, 탐색, 삽입, 삭제 시 <strong>로그 시간 복잡도(O(log n))</strong>보장</li>
</ul>
<h4 id="📍-b-tree-인덱스의-구조">📍 B-Tree 인덱스의 구조</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/f9501e99-c5ff-4d9a-a4ec-5e9f4215e343/image.png" /></p>
<ul>
<li>루트 노드 → 내부 노드(브랜치 노드) → 리프 노드</li>
<li><strong>모든 노드</strong>에 key와 그에 해당하는 데이터(data 또는 데이터의 포인터) 저장</li>
<li>키 범위 탐색 가능(WHERE age &gt; 30 AND age &lt; 50)</li>
</ul>
<h4 id="📍-b-tree-인덱스의-특징">📍 B-Tree 인덱스의 특징</h4>
<ul>
<li>범위 조회, 정렬, ORDER BY, BETWEEN 등에 강력</li>
<li>대부분의 일반적인 인덱스는 내부적으로 B-TREE로 구현된다</li>
<li>B-tree의 생성 당시는 균형 트리이지만 테이블 갱신(INSERT/UPDATE/DELETE)의 반복을 통해 서서히 균형이 깨지고, 성능이 악화된다. </li>
</ul>
<hr />
<h3 id="✅-2-btree-인덱스balanced-tredd-index">✅ 2. B+Tree 인덱스(Balanced Tredd Index)</h3>
<h4 id="📍-btree-인덱스란">📍 B+Tree 인덱스란?</h4>
<ul>
<li><strong>B-tree의 확장개념</strong></li>
<li>트리 기반 인덱스 중 하나이며, 실무에서 가장 널리 사용되는 인덱스 구조</li>
</ul>
<h4 id="📍-btree의-구조">📍 B+Tree의 구조</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/32330b09-5a1c-45e9-b9d0-8d3e3396907a/image.png" /></p>
<ul>
<li>내부 노드(브랜치 노드)에 <strong>key만 담아두고, data는 담지 않는다.</strong>(검색용 키만 포함)</li>
<li><strong>오직 리프 노드에만 key와 data만 저장</strong>하며, 리프 노드끼리 Linked list로 연결되어 있다.</li>
</ul>
<h4 id="📍-btree-인덱스의-특징">📍 B+Tree 인덱스의 특징</h4>
<ul>
<li>대부분의 관계형 데이터베이스(RDBMS)는 B-Tree라고 표기하지만, 실제로는 <strong>성능상 이점</strong>을 위해 B+Tree로 내부 구현하고 있다.</li>
<li>모든 데이터를 리프 노드에 저장하고, 내부 노드는 검색 키만 가지는 <strong>균형트리 기반</strong> 인덱스 구조</li>
<li>리프 노드가 연결되어 있어 <strong>순차 탐색</strong>이 빠르며, 모든 데이터는 리프 노드에 존재하므로 디스크 접근 예측이 가능하다.</li>
</ul>
<hr />
<h3 id="✅-3-해시-인덱스hash-index">✅ 3. 해시 인덱스(Hash Index)</h3>
<h4 id="📍-해시-인덱스란">📍 해시 인덱스란?</h4>
<ul>
<li>키 값을 해시 <strong>함수로 변환</strong>하여 저장</li>
<li>데이터가 정렬되지 않음 → <strong>순차 접근 불</strong>가</li>
<li><strong>MySQL Memory 엔진</strong> 등에서 사용됨 (InnoDB는 해시 인덱스 미지원)<ul>
<li>일반적인 DBMS에서 메모리 기반의 테이블에 주로 구현 </li>
<li>디스크 대용량 테이블 용으로는 거의 사용되지 않음<h4 id="📍-해시-인덱스의-구조">📍 해시 인덱스의 구조</h4>
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/3f973c3d-95b3-49f2-b34e-0f91e4ddbe9a/image.png" /></li>
<li>Key → Hash → Bucket → Pointer 형태</li>
<li>검색하고자하는 값을 주면 해시 함수를 거쳐** 찾고자하는 키 값이 포함된 버켓**을 찾아냄<h4 id="📍-해시-인덱스의-특징">📍 해시 인덱스의 특징</h4>
</li>
<li><strong>실제 저장 위치</strong>를 빠르게 알 수 있음</li>
<li>원래의 키 값이 아닌 함수 결과에 대한 값을 저장하기 때문에 <strong>저장 용량 감소</strong></li>
<li><strong>범위 검색</strong>이나 원본 값 기준 <strong>정렬 불가</strong></li>
</ul>
</li>
</ul>
<hr />
<h3 id="✅-4-클러스터드-인덱스clustered-index">✅ 4. 클러스터드 인덱스(Clustered Index)</h3>
<h4 id="📍-클러스터드-인덱스란">📍 클러스터드 인덱스란?</h4>
<ul>
<li>인덱스의 리프 노드가 실제 데이터 자체를 포함함
→ 즉, <strong>인덱스의 정렬 기준</strong>에 따라 테이블의 행(Row)들이 디스크에 물리적으로 정렬<ul>
<li>MySQL InnoDB에서는 <strong>기본키(PK)가 클러스터드 인덱스</strong></li>
</ul>
</li>
</ul>
<h4 id="📍-클러스터드-인덱스의-구조">📍 클러스터드 인덱스의 구조</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/850d3745-022a-404f-a840-f15670b59383/image.png" /></p>
<ul>
<li>B-트리 리프 노드 = 테이블의 실제 데이터</li>
<li>행 데이터를 해당 열로 정렬한 후에 루트 페이지를 생성한다.</li>
<li>즉, 데이터페이지는 리프 노드와 같다.</li>
<li>책의 페이지를 알기 때문에 그 페이지를 바로 펴는것과 같다.</li>
</ul>
<h4 id="📍-클러스터드-인덱스의-특징">📍 클러스터드 인덱스의 특징</h4>
<ul>
<li>테이블 당 <strong>1개만 존재 가능</strong></li>
<li>물리적으로 <strong>행을 재배열</strong></li>
<li>PK가 존재할 경우, <strong>기본키가 클러스터드 인덱스로 설정</strong>됨</li>
<li>인덱스 자체의 리프 페이지가 곧 데이터임
→ 즉, <strong>테이블 자체가 인덱스</strong></li>
<li>논클러스터드 인덱스보다 검색속도는 더 빠르다. 하지만 데이터의 입력, 수정, 삭제는 느리다.</li>
</ul>
<h3 id="✅-5-비클러스터드-인덱스-non-clustered-index">✅ 5. 비클러스터드 인덱스 (Non-Clustered Index)</h3>
<h4 id="📍-비클러스터드-인덱스란">📍 비클러스터드 인덱스란?</h4>
<ul>
<li><strong>리프 노드에 실제 데이터의 주소(ROWID, PK 등)</strong>만 저장</li>
<li>데이터는 <strong>인덱스와 별도로 저장</strong>, 정렬되지 않음</li>
<li>뒤에 목차에서 찾고자 하는 내용의 페이지를 찾고 그 페이지로 이동하는 것과 같다.</li>
</ul>
<h4 id="📍-비클러스터드-인덱스의-구조">📍 비클러스터드 인덱스의 구조</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/b1c0d805-c0e8-456c-b85d-8f914da0d921/image.png" /></p>
<ul>
<li>B-트리 리프 노드 = (인덱스 키, 실제 데이터 주소)</li>
<li>데이터 페이지를 건들지 않고, 별도의 장소에** 인덱스 페이지를 생성**</li>
<li>인덱스 페이지의 리프 페이지에 인덱스로 구성한 열을 정렬하고 <strong>데이터 위치 포인터</strong>를 생성</li>
<li>데이터의 위치 포인트는 '<strong>페이지 번호 + #오프셋</strong>'이 기록되어 바로 데이터 위치를 가리킴</li>
<li>예)indexTest2는 102번 페이지의 두번째(#2)에 데이터가 있다고 기록</li>
</ul>
<h4 id="📍-비클러스터드-인덱스의-특징">📍 비클러스터드 인덱스의 특징</h4>
<ul>
<li>테이블당 약 <strong>240개의 인덱스 생성 가능</strong></li>
<li>인덱스 페이지는 <strong>로그파일</strong>에 저장됨</li>
<li>레코드의 원본은 정렬되지 않고, <strong>인덱스 페이지만 정렬</strong>된다.</li>
<li>인덱스 자체의 리프 페이지는 데이터가 아니라 데이터가 위치하는 포인터이기 때문에 클러스터형 보다 검색 속도는 더 느리지만 데이터의 입력, 수정, 삭제는 더 빠르다</li>
</ul>
<hr />
<h3 id="☑️-b-tree-vs-btree">☑️ B-Tree vs B+Tree</h3>
<table>
<thead>
<tr>
<th>항목</th>
<th>B-Tree</th>
<th>B+Tree</th>
</tr>
</thead>
<tbody><tr>
<td><strong>데이터 저장 위치</strong></td>
<td>모든 노드에 저장</td>
<td>리프 노드에만 저장</td>
</tr>
<tr>
<td><strong>내부 노드</strong></td>
<td>키 + 데이터 포함 가능</td>
<td>키만 포함 (데이터 없음)</td>
</tr>
<tr>
<td><strong>리프 노드 간 연결</strong></td>
<td>없음 → 순차 탐색 불리</td>
<td>수평 연결 → 범위 탐색 유리</td>
</tr>
<tr>
<td><strong>검색 성능</strong></td>
<td>빠름 (단일 키)</td>
<td>빠름 + 범위/정렬 효율 높음</td>
</tr>
<tr>
<td><strong>트리 높이</strong></td>
<td>상대적으로 낮음</td>
<td>약간 더 높음 (데이터가 리프에만 존재하므로)</td>
</tr>
<tr>
<td><strong>사용 예</strong></td>
<td>파일 시스템, 메모리 캐시</td>
<td>RDBMS 인덱스 (MySQL, Oracle 등)</td>
</tr>
<tr>
<td><strong>범위 검색</strong></td>
<td>비효율적</td>
<td>매우 효율적(<code>BETWEEN</code>, <code>ORDER BY</code> 등)</td>
</tr>
</tbody></table>
<blockquote>
<ul>
<li>B-Tree는 데이터가 분산되어 있고 <strong>검색에 불리</strong></li>
</ul>
</blockquote>
<ul>
<li>B+Tree는 정렬성과 순차 탐색에 뛰어나서 <strong>DB 인덱스에 최적화</strong></li>
</ul>
<hr />
<h3 id="☑️-클러스터드-인덱스-vs-비클러스터드-인덱스">☑️ 클러스터드 인덱스 vs 비클러스터드 인덱스</h3>
<table>
<thead>
<tr>
<th>항목</th>
<th>클러스터드 인덱스 (Clustered)</th>
<th>비클러스터드 인덱스 (Non-Clustered)</th>
</tr>
</thead>
<tbody><tr>
<td><strong>리프 노드</strong></td>
<td>데이터 자체(row) 저장</td>
<td>데이터의 주소(pointer) 저장</td>
</tr>
<tr>
<td><strong>테이블 정렬</strong></td>
<td>정렬됨(인덱스 기준)</td>
<td>정렬되지 않음</td>
</tr>
<tr>
<td><strong>테이블당 개수</strong></td>
<td>1개만 허용</td>
<td>여러 개 가능</td>
</tr>
<tr>
<td><strong>검색 속도</strong></td>
<td>빠름 (PK 검색, 범위 조회, ORDER BY)</td>
<td>일반적으로 느림 (주소 → 테이블 2단계 조회)</td>
</tr>
<tr>
<td><strong>실제 데이터 위치</strong></td>
<td>인덱스 리프 노드 = row</td>
<td>인덱스 리프 → row 위치로 별도 조회 필요</td>
</tr>
<tr>
<td><strong>실무 예시</strong></td>
<td>PK, 자주 범위 검색 되는 컬럼</td>
<td>WHERE, JOIN, GROUP BY 대상이 자주 바뀌는 컬럼</td>
</tr>
<tr>
<td><strong>InnoDB 기본 설정</strong></td>
<td>PK → 클러스터드 인덱스</td>
<td>나머지 인덱스들</td>
</tr>
</tbody></table>
<hr />
<h2 id="📗인덱스의-장점과-단점">📗인덱스의 장점과 단점</h2>
<h3 id="✅-장점">✅ 장점</h3>
<ul>
<li>테이블 조회 속도 및 그에 따른 성능 향상</li>
<li>전반적인 시스템의 부하를 줄일 수 있다.<h3 id="✅-단점">✅ 단점</h3>
</li>
<li>인덱스를 관리하기 위한 추가 저장공간 필요<ul>
<li>인덱스를 관리하기 위한 추가 작업 필요</li>
<li>인덱스를 잘못 사용할 경우 오히려 성능이 저하되는 역효과가 발생 할 수 있다.</li>
</ul>
</li>
</ul>
<hr />
<h2 id="📗인덱스를-사용하면-좋은-경우">📗인덱스를 사용하면 좋은 경우</h2>
<ul>
<li>규모가 작지 않은 테이블</li>
<li>INSERT, UPDATE, DELETE가 자주 발생하지 않는 컬럼</li>
<li>JOIN이나 WHERE 또는 ORDER BY에 자주 사용되는 컬럼</li>
<li>데이터의 중복도가 낮은 컬럼</li>
</ul>