<h1 id="🔍이진-탐색-트리bst-binary-search-tree">🔍이진 탐색 트리(BST, Binary Search Tree)</h1>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/accc5a82-1a92-44bd-be93-b6394edfab4f/image.png" /></p>
<blockquote>
<p>이진 트리의 특수한 형태로, 이진 탐색이 효율적으로 동작할 수 있도록 고안된 자료구조의 일종. <strong>정렬된 이진 트리</strong></p>
</blockquote>
<h2 id="🧭이진-탐색-트리의-특징">🧭이진 탐색 트리의 특징</h2>
<ul>
<li>왼쪽 서브트리에는 <strong>부모 노드보다 작은 값</strong>이 위치</li>
<li>오른쪽 서브트리에는 <strong>부모 노드보다 큰 값</strong>이 위치</li>
<li>중복된 키를 허용 하지 않는다.</li>
</ul>
<h2 id="📊이진-탐색-트리의-시간-복잡도">📊이진 탐색 트리의 시간 복잡도</h2>
<h4 id="👉탐색삽입삭제">👉탐색/삽입/삭제</h4>
<ul>
<li>평균 : *<em>O(log n) *</em>→ 트리가 균형 잡힌 상태일 때</li>
<li>최악 : <strong>O(n)</strong> → 트리가 한쪽으로 치우친 경우(사실상 연결 리스트)</li>
</ul>
<hr />
<h2 id="✅이진-탐색-트리의-검색">✅이진 탐색 트리의 검색</h2>
<blockquote>
<ol>
<li>루트 노드부터 방문 하여 탐색을 진행</li>
<li>검색 값이 루트보다 작으면 왼쪽으로, 크다면 오른쪽을 방문</li>
<li>일치하는 값을 찾을때까지 절차 반복</li>
<li>검색 값이 없으면 null 반환 </li>
</ol>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/1b80b9de-f6e2-4793-a50b-471c6eefdf8f/image.png" /></p>
<hr />
<h2 id="✅이진-탐색-트리의-생성">✅이진 탐색 트리의 생성</h2>
<blockquote>
<p>*<em>50, 15, 62, 80, 7, 54, 11 *</em>의 요소로 이진트리를 생성한다고 했을때 과정은 다음과 같다.</p>
</blockquote>
<ol>
<li>50을 트리의 루트에 삽입한다.</li>
<li>다음 요소를 읽고 루트 노드 요소보다 작으면 왼쪽 하위 트리의 루트로 삽입</li>
<li>루트 노드의 요소보다 크면 오른쪽 하위 트리의 루트로 삽입</li>
</ol>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/aab976ee-f7e4-45ac-9d20-54a4b68b6c66/image.png" /></p>
<hr />
<h2 id="✅이진-탐색-트리의-삽입">✅이진 탐색 트리의 삽입</h2>
<blockquote>
<ol>
<li>루트에서 시작</li>
<li>삽입 값을 루트와 비교하여 루트보다 작으면 왼쪽, 크다면 오른쪽</li>
<li>리프 노드에 도달 한 후, 노드보다 크면 오른쪽, 작다면 왼쪽에 삽입</li>
</ol>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/62da3b40-9aaa-4222-9eea-78980aaccbbd/image.png" /></p>
<hr />
<h2 id="✅이진-탐색-트리의-삭제">✅이진 탐색 트리의 삭제</h2>
<h3 id="1-삭제할-노드가-리프-노드인-경우">1. 삭제할 노드가 리프 노드인 경우</h3>
<blockquote>
<p>바로 삭제</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/cfe3352d-c481-4156-95b2-89eca868974a/image.png" /></p>
<hr />
<h3 id="2-삭제할-노드에-자식이-하나만-있는-경우">2. 삭제할 노드에 자식이 하나만 있는 경우</h3>
<blockquote>
<p>노드를 삭제하고 자식 노드를 삭제된 노드의 부모에 직접 연결</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/14de9743-3cfa-4ff8-82b7-25c4874ae38f/image.png" /></p>
<hr />
<h3 id="3-삭제할-노드에-자식이-둘-있는-경우">3. 삭제할 노드에 자식이 둘 있는 경우</h3>
<blockquote>
<p>자식이 둘 있는 경우 successor 노드를 찾는 과정이 추가된다.
successor 노드란 오른쪽 서브트리의 최소값을 말한다.</p>
</blockquote>
<ol>
<li>삭제할 노드를 찾는다.</li>
<li>삭제할 노드의 successor 노드를 찾는다</li>
<li>삭제할 노드와 successor 노드의 값을 바꾼다</li>
<li>노드를 삭제한다.</li>
</ol>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/f3d62bf5-aaad-4b00-8099-d33607582a46/image.png" /></p>
<hr />
<blockquote>
<p>BST는 한쪽으로 치우치면 성능이 O(n)이라는 단점이 있는데, 이런 단점을 해결하기 위해 나온 균형 이진 탐색 트리 중 대표적인 트리로 AVL 트리와 레드-블랙 트리(RBT)가 있다. 이것도 같이 알아보자.</p>
</blockquote>
<h1 id="💡avl-트리-adelson-velsky-and-landis-tree">💡AVL 트리 (Adelson-Velsky and Landis Tree)</h1>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/658247ba-34bc-450a-b84c-f8077aacdb6b/image.png" /></p>
<ul>
<li>최초로 제안된 자기 균형 이진 탐색 트리로, 말단 노드의 레벨 차이가 항상 1이하로 나는 트리이다.</li>
<li>노드 삽입/삭제 후 자동으로 균형을 유지하기 위해 회전 연산을 사용하며, 회전 연산은 트리의 구조를 변경하여 균형을 복구한다.</li>
<li><strong>AVL 트리는 탐색/삽입/삭제 연산의 시간 복잡도가 모두 O(log n)</strong>이다.</li>
<li>균형을 유지하기 위해 회전 연산이 필요하므로 이진 탐색 트리보다는 조금 구현이 복잡하다.</li>
<li>자주 변경되는 데이터 셋에 적합하며 데이터의 삽입과 삭제가 빈번한 경우에도 항상 빠른 탐색 속도를 제공한다.</li>
</ul>
<hr />
<h1 id="💡레드-블랙-트리red-black-tree-rbt">💡레드-블랙 트리(Red-Black Tree, RBT)</h1>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/c0eb8210-742f-4bd9-8fa9-cd577361e62f/image.png" /></p>
<ul>
<li>모든 노드는 Red / Black의 색을 가져야한다.</li>
<li>루트 노드는 항상 블랙이다.</li>
<li>NIL 노드는 항상 블랙이다.(value가 없는 말단 노드를 항상 가진다. NIL은 Null Leaf를 뜻함)</li>
<li>레드 노드의 자식 노드는 항상 블랙이다.</li>
<li>각 노드에서 말단 노드로 가는 블랙 노드의 수는 항상 같아야 한다.</li>
</ul>
<p><strong>👉 위 다섯가지 규칙을 만족하는 트리가 레드-블랙 트리이다.</strong></p>
<ul>
<li>레드-블랙 트리는 완벽한 균형은 아니며, 삽입/삭제 시 색 변경과 회전으로 균형을 유지한다.</li>
<li><strong>탐색/삽입/삭제 모두 O(log n)을 보장한다.</strong></li>
<li>삽입/삭제 시 회전 횟수가 적으며, 실제 시스템에서 자주 쓰인다.</li>
</ul>
<hr />
<h1 id="📊-avl-vs-rbt-비교">📊 AVL vs RBT 비교</h1>
<table>
<thead>
<tr>
<th>구분</th>
<th>AVL 트리 🌲</th>
<th>레드-블랙 트리 ⚫🔴</th>
</tr>
</thead>
<tbody><tr>
<td>균형 유지</td>
<td>엄격 (높이 차 ≤ 1)</td>
<td>느슨 (경로별 Black 수 유지)</td>
</tr>
<tr>
<td>탐색 속도</td>
<td>빠름 (더 균형적)</td>
<td>보통 (조금 덜 균형)</td>
</tr>
<tr>
<td>삽입/삭제</td>
<td>느림 (회전 많음)</td>
<td>빠름 (회전 적음)</td>
</tr>
<tr>
<td>활용 예시</td>
<td>데이터베이스 인덱스</td>
<td>언어 라이브러리, 커널 코드</td>
</tr>
</tbody></table>
<blockquote>
<p><strong>AVL 트리: 탐색 위주 → 항상 엄격하게 균형 유지
RBT: 삽입/삭제 많은 경우 → 유지 비용 최소화</strong></p>
</blockquote>
<p><strong>[참고] AVL과 RBT 모두 O(log n)를 보장하지만, AVL은 더 엄격한 균형으로 탐색이 빠른 대신 삽입/삭제 유지비용이 크고, RBT는 느슨한 균형으로 삽입/삭제가 평균적으로 더 안정적이다!</strong></p>