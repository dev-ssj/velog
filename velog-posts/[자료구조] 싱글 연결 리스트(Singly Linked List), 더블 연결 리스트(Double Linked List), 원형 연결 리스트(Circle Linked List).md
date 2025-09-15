<blockquote>
<p>기술 면접 스터디 중 싱글, 더블, 원형 연결 리스트의 개념이 애매해서 정리해는 시간을 갖는다!</p>
</blockquote>
<h2 id="1️⃣싱글-연결-리스트singly-linked-list">1️⃣싱글 연결 리스트(Singly Linked List)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/42a4f98f-d392-49da-870a-fd907b58ea16/image.png" /></p>
<ul>
<li><strong>구조</strong> : 각 노드가 <strong>데이터 + 다음 노드 포인터(next)</strong>만 가진다.</li>
<li><strong>특징</strong><ul>
<li>앞에서 뒤로 <strong>단방향 탐색</strong>만 가능</li>
<li>메모리 사용이 적음 (<strong>포인터 1개</strong>).</li>
<li>삽입/삭제가 특정 위치(앞, 중간)에서 배열보다 유리</li>
</ul>
</li>
<li><strong>단점</strong><ul>
<li>역방향 탐색 불가</li>
<li>중간 노드 삭제 시 이전 노드 탐색이 필요 → <strong>O(n)</strong></li>
</ul>
</li>
<li><strong>사용 예시</strong><ul>
<li><strong>단순 큐</strong> 구현</li>
<li>가비지 컬렉션</li>
</ul>
</li>
</ul>
<hr />
<h2 id="2️⃣더블-연결-리스트-doubly-linked-list">2️⃣더블 연결 리스트 (Doubly Linked List)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/db7c2f99-3ca1-48b3-983e-b97173407c8c/image.png" /></p>
<ul>
<li><strong>구조</strong> : 각 노드가 <strong>데이터 + 이전(prev) 포인터 +다음(next) 포인터</strong>를 가진다</li>
<li><strong>특징</strong><ul>
<li>앞/뒤 <strong>양방향 탐색</strong> 가능</li>
<li>삽입/삭제 시 이전 노드를 몰라도 <strong>O(1)</strong>로 가능(포인터만 조작하면 된다)</li>
</ul>
</li>
<li><strong>단점</strong><ul>
<li>메모리 사용량 증가(<strong>포인터 2개 필요</strong>)</li>
<li>구현 복잡도 상승</li>
</ul>
</li>
<li><strong>사용 예시</strong><ul>
<li><strong>Deque</strong>, <strong>LRU Cache</strong>(양방향 탐색, 중간 삭제가 빠른 자료구조 필요할 때)</li>
<li>운영체제의 프로세스 관리(프로세스 제어 블록을 양방향 리스트로 연결)</li>
</ul>
</li>
</ul>
<hr />
<h2 id="3️⃣원형-연결-리스트-circular-linked-list">3️⃣원형 연결 리스트 (Circular Linked List)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/3d15589e-d7d3-471c-971d-1bcbcfae7bad/image.png" /></p>
<ul>
<li><strong>구조</strong> : <strong>마지막 노드의 포인터가 처음 노드(head)</strong>를 가리킴</li>
<li><strong>특징</strong><ul>
<li>끝과 처음이 이어져 있다 → <strong>순환 구조</strong></li>
<li>싱글/더블 모두 원형으로 구현 가능</li>
</ul>
</li>
<li><strong>단점</strong><ul>
<li>순회 중 무한 루프 주의 필요</li>
<li>구현이 상대적으로 복잡</li>
</ul>
</li>
<li><strong>사용 예시</strong><ul>
<li>라운드 로빈(Round Robin) 스케줄링 (운영체제)</li>
<li>버퍼 구조(<strong>원형 큐</strong>)</li>
</ul>
</li>
</ul>
<hr />
<h2 id="📊비교">📊비교</h2>
<table>
<thead>
<tr>
<th>자료구조</th>
<th>포인터 개수</th>
<th>탐색 방향</th>
<th>장점</th>
<th>단점</th>
<th>대표 활용</th>
</tr>
</thead>
<tbody><tr>
<td>싱글 링크드 리스트</td>
<td>1개 (next)</td>
<td>한 방향만</td>
<td>구현 간단, 메모리 절약</td>
<td>역방향 탐색 불가</td>
<td>단순 큐</td>
</tr>
<tr>
<td>더블 링크드 리스트</td>
<td>2개 (prev, next)</td>
<td>양방향</td>
<td>양방향 탐색 가능, 중간 삭제 용이</td>
<td>메모리 사용 많음</td>
<td>LRU Cache, Deque</td>
</tr>
<tr>
<td>원형 링크드 리스트</td>
<td>1~2개 (구현 방식)</td>
<td>한 방향/양방향 순환</td>
<td>순환 구조 활용</td>
<td>무한 루프 주의</td>
<td>라운드 로빈 스케줄링</td>
</tr>
</tbody></table>
<br />

<table>
<thead>
<tr>
<th>연산</th>
<th>싱글 링크드 리스트</th>
<th>더블 링크드 리스트</th>
<th>원형 링크드 리스트 (단일/이중)</th>
</tr>
</thead>
<tbody><tr>
<td>탐색 (Search)</td>
<td>O(n)</td>
<td>O(n)</td>
<td>O(n)</td>
</tr>
<tr>
<td>삽입 (앞/뒤)</td>
<td>O(1) (head/tail 참조 시)</td>
<td>O(1) (head/tail 참조 시)</td>
<td>O(1) (head/tail 참조 시)</td>
</tr>
<tr>
<td>삽입 (중간)</td>
<td>O(n) (위치 탐색 후 O(1))</td>
<td>O(n) (위치 탐색 후 O(1))</td>
<td>O(n) (위치 탐색 후 O(1))</td>
</tr>
<tr>
<td>삭제 (앞/뒤)</td>
<td>O(1) (head 삭제 쉬움, tail 삭제는 O(n) 필요)</td>
<td>O(1) (head/tail 모두 가능)</td>
<td>O(1) (head/tail 모두 가능)</td>
</tr>
<tr>
<td>삭제 (중간)</td>
<td>O(n) (이전 노드 필요)</td>
<td>O(1) (노드 참조 시 prev/next만 연결 변경)</td>
<td>O(1) (노드 참조 시 가능)</td>
</tr>
<tr>
<td>순회 (Traversal)</td>
<td>O(n)</td>
<td>O(n)</td>
<td>O(n) (순환 구조라 무한 루프 주의)</td>
</tr>
</tbody></table>
<hr />
<h2 id="📌lru-cacheleast-recently-used-cache">📌LRU Cache(Least Recently Used Cache)</h2>
<ul>
<li><strong>정의</strong> : <strong>가장 오랫동안 사용되지 않은 데이터를 제거</strong>하는 캐시 알고리즘</li>
<li><strong>구현 방식</strong><ul>
<li><strong>HashMap + 더블 연결 리스트</strong> 조합<ul>
<li>HashMap → 키(key)로 데이터 O(1) 조회<ul>
<li>Doubly Linked List → 최근 사용된 순서 관리(앞 : 최신, 뒤: 오래된)</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><strong>시간 복잡도</strong><ul>
<li>조회 : O(1)</li>
<li>삽입/갱신 : O(1)</li>
</ul>
</li>
<li><strong>활용 예시</strong><ul>
<li>웹 브라우저 캐시</li>
<li>데이터베이스 페이지 교체</li>
<li>메모리 캐싱 시스템</li>
</ul>
</li>
<li><em>👉 LRU는 더블 링크드 리스트 + 해시맵 조합의 대표 사례*</em></li>
</ul>