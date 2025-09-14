<h1 id="🌳-트리tree란">🌳 트리(Tree)란?</h1>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/ce3f5659-769e-4694-becf-59d5a0e734b6/image.png" /></p>
<blockquote>
<p>트리란 <strong>계층적인 구조를 나타내는 자료구조</strong>로, <strong>노드(Node)</strong>와 노드를 연결해주는 <strong>간선(Edge)</strong>로 이루어져 있다. 노드의 개수가 N개일 때, 간선의 개수는 항상 N-1 이다.</p>
</blockquote>
<hr />
<h2 id="💭트리에서-사용되는-용어들">💭트리에서 사용되는 용어들</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/c9c2418c-2770-43a6-8c1b-9585948f74ce/image.png" /></p>
<p><strong>1. 루트 (Root) ** : 최상단 노드 (A)
**2. 자식(Child)</strong> : 부모(Parent)를 가진 노드 (B, D, E, C, F, G, H)
<strong>3. 리프(Leaf) *<em>: 자식이 없는 말단 노드 (D, E, G, H)
*</em>4. 내부 노드(internal node)</strong> : 자식 노드가 하나라도 존재하는 노드로, 리프 노드가 아닌 노드 (A,B,C,F)
<strong>5. 형제(sibling)</strong> : 같은 레벨에 위치한 노드들의 관계 (B-D, C-D-F, G-H)
<strong>6. 조상(ancestor)-후손(descendant)</strong> : (D의 조상은 A, B이며, H의 조성은 F, C, A)
<strong>7. 차수(degree)</strong> : 자식 노드의 개수 (F의 차수는 2, G의 차수는 0)
<strong>8. 레벨(level)</strong> : 루트 노드로부터 얼마나 떨어져있는지를 나타냄 (B와 C의 레벨은 1, D의 레벨은 2, G의 레벨은 3)
<strong>9. 높이(height)</strong> : 루트 노드부터 시작해서 리프 노드까지의 거리  (예시 트리의 높이는 4)
<strong>10. 서브트리(subtree)</strong> : 트리 안의 트리. 특정 노드들을 기준으로 하여 해당 노드의 후손들로 구성한 트리</p>
<hr />
<h1 id="🔄트리-순회">🔄트리 순회</h1>
<blockquote>
<p>트리의 순회란 <strong>정해진 순서에 따라 트리의 모든 노드를 한번씩 방문하는 작업</strong>을 말한다.
<strong>깊이 우선 탐색</strong>과 <strong>너비 우선 탐색</strong>으로 나뉜다.</p>
</blockquote>
<hr />
<h2 id="깊이-우선-탐색dfs-depth-firsh-search">깊이 우선 탐색(DFS, Depth-Firsh Search)</h2>
<blockquote>
<p><strong>왼쪽/오른쪽 자식을 따라 끝까지 내려갔다가 다시 올라오는 방식</strong>
Root 기준이라는 것을 잊지말것!</p>
</blockquote>
<h3 id="1-전위-순회pre-order-traverse">1. 전위 순회(Pre-order traverse)</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/895d2611-0bbb-4e43-99be-268393465a01/image.png" /></p>
<ul>
<li>순서 : <strong>Root → Left → Right</strong></li>
<li>예시 트리 순서 : A → B → D → E → C → F → G → H</li>
</ul>
<h3 id="2-중위-순회in-order-traverse">2. 중위 순회(In-order traverse)</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/895d2611-0bbb-4e43-99be-268393465a01/image.png" /></p>
<ul>
<li>순서 : <strong>Left → Root → Right</strong></li>
<li>예시 트리 순서 : D → B → E → A → C → G → F → H</li>
</ul>
<h3 id="3-후위-순회postorder-traverse">3. 후위 순회(Postorder traverse)</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/895d2611-0bbb-4e43-99be-268393465a01/image.png" /></p>
<ul>
<li>순서 : <strong>Left → Right → Root</strong></li>
<li>예시 트리 순서 : D → E → B → G → H → F → C → A</li>
</ul>
<hr />
<h2 id="너비-우선-탐색bfs-breadth-first-search">너비 우선 탐색(BFS, Breadth-First Search)</h2>
<blockquote>
<p>레벨 단위로 방문(큐 사용)</p>
</blockquote>
<h3 id="1-레벨-순회level-order-traverse">1. 레벨 순회(Level-order traverse)</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/34bf6f13-0ad6-4f17-ade6-9d9709e81172/image.png" /></p>
<ul>
<li>순서 : 위에서 아래, 왼쪽에서 오른쪽. <strong>낮은 레벨에 있는 노드들 모두 방문 후 다음 레벨로 이동</strong></li>
<li>예시 트리 순서 : A → B → C → D → E → F → G → H</li>
</ul>
<hr />
<h1 id="🌲이진-트리binary-tree">🌲이진 트리(Binary Tree)</h1>
<blockquote>
<p>이진 트리는 트리의 한 종류로써, 모든 노드가 최대 두 개의 자식을 가지는 트리이다.</p>
</blockquote>
<ul>
<li>모든 노드의 차수가 2 이하다.</li>
<li>모든 노드가 2개의 서브 트리를 가지고 있다.</li>
</ul>
<h2 id="1-정-이진-트리full-binary-tree">1. 정 이진 트리(Full binary tree)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/d7d778bd-db57-4eec-a96f-15b7b947beb5/image.png" /></p>
<ul>
<li><strong>모든 노드가 0개 또는 2개의 자식 노드를 가진 이진 트리</strong>이다.</li>
<li>리프 노드를 제외한 모든 노드의 자식 노드가 2개</li>
</ul>
<h2 id="2-포화-이진-트리perfect-binary-tree">2. 포화 이진 트리(Perfect binary tree)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/43f5e614-b109-4649-98ac-428838bed156/image.png" /></p>
<ul>
<li><strong>트리의 모든 레벨에서 노드가 모드 채워져 있는 이진 트리</strong>이다.</li>
</ul>
<h2 id="3-완전-이진-트리complete-binary-tree">3. 완전 이진 트리(Complete binary tree)</h2>
<ul>
<li>마지막 레벨을 제외한 모든 레벨에 노드가 완전히 채워져 있고, <strong>마지막 레벨에는 노드가 왼쪽부터 채워져 있는 이진 트리이다.</strong></li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/f7fe130e-f00c-4609-a2bf-b540eb211d29/image.png" /></p>
<h2 id="4-편향-트리skewed-tree">4. 편향 트리(Skewed tree)</h2>
<ul>
<li><strong>리프노드를 제외한 모든 노드가 하나의 자식 노드만 가지는 트리</strong></li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/a1675c37-6034-4801-bb21-06dd21be70d6/image.png" /></p>