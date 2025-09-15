<h1 id="💡그래프graph란">💡그래프(Graph)란?</h1>
<blockquote>
<p>그래프란 요소들이 서로 복잡하게 연결되어 있는 관계를 표현하는 자료구조로, <strong>정점(노드)</strong>과 <strong>간선</strong>들의 집합으로 구성된다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/de4b6c14-cf7e-415d-9136-653484664ecb/image.png" /></p>
<h2 id="✅그래프-관련-용어">✅그래프 관련 용어</h2>
<ul>
<li><strong>정점(vertex)</strong> : 노드라고도하며, 데이터가 저장되는 그래프의 기본 원소이다</li>
<li><strong>간선(edge)</strong> : 링크,arcs라고도 불리며, 노드 간의 관계를 나타낸다.</li>
<li><strong>인접 정점(adjacent vertex)</strong> : 간선에 의해 직접 연결되어 있는 정점 (<code>A-B</code>, <code>A-C</code>, <code>C-D</code>, <code>A-D</code>) </li>
<li><strong>차수(degree)</strong> : 무방향 그래프에서 하나의 정점에 연결된 간선의 수를 말한다.(정점 A의 차수 : 3)</li>
<li><strong>경로(path)</strong> : 간선을 따라갈 수 있는 길을 뜻하며, 정점을 나열하여 표시한다.</li>
<li><strong>경로의 길이(length)</strong> : 경로를 구성하는데 사용된 간선의 수를 뜻한다.</li>
<li><strong>단순 경로(simple path)</strong> : 경로 중에서 반복되는 간선이 없는 경로를 뜻한다.</li>
<li><strong>사이클(cycle)</strong> : 시작 정점과 종료 정점이 같은 단순 경로를 뜻한다(<code>A-C-D-A</code> 경로)</li>
</ul>
<hr />
<h2 id="💭그래프를-표현하는-방법">💭그래프를 표현하는 방법</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/f151e3a2-f6bb-4de0-b84a-9fece2c9e66d/image.png" /></p>
<ul>
<li><strong>V(G1)</strong> = {0,1,2,3}, <strong>E(G1)</strong> = {(0,1),(0,2),(0,3),(1,2),(2,3)}</li>
<li><strong>V(G2)</strong> = {0,1,2,3}, <strong>E(G2)</strong> = {(0,1),(0,2)}</li>
<li><strong>V(G3)</strong> = {0,1,2}, *<em>E(G3) *</em>= {&lt;0,1&gt;,&lt;1,0&gt;,&lt;1,2&gt;}</li>
</ul>
<hr />
<h2 id="📋그래프의-종류">📋그래프의 종류</h2>
<h3 id="1-무방향성-그래프undirected-graph">1. 무방향성 그래프(Undirected Graph)</h3>
<ul>
<li>무방향성을 가진 그래프</li>
<li>무방향 그래프는 모든 간선이 양방향</li>
</ul>
<h3 id="2-유방향성-그래프directed-graph-digraph">2. 유방향성 그래프(Directed Graph, digraph)</h3>
<ul>
<li>방향성을 가진 그래프</li>
<li>방향성 그래프는 모든 간선이 단방향</li>
</ul>
<h3 id="3-가중치-그래프weighted-graph">3. 가중치 그래프(Weighted Graph)</h3>
<ul>
<li>각 간선마다 가중치 또는 비용이 할당된 그래프
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/5f455b4f-a254-4f37-aefd-cdd6bc4c7e14/image.png" /></li>
</ul>
<h3 id="4-연결-그래프connected-graph--비연결-그래프disconnected-graph">4. 연결 그래프(Connected Graph) / 비연결 그래프(Disconnected Graph)</h3>
<ul>
<li>모든 정점이 연결되어 있으면 연결그래프</li>
<li>그렇지 않으면 비연결 그래프
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/582c0c54-ef90-44dc-bb4d-3c6dde8dabb7/image.png" /></li>
<li>G1은 모든 정점이 연결되어 있으므로 연결 그래프</li>
<li>G2는 {0,1}, {2,3,4,5)가 연결되어 있지 않아 비연결 그래프</li>
</ul>
<h3 id="5-순환-그래프cyclic-graph--비순환-그래프acyclic-graph">5. 순환 그래프(Cyclic Graph) / 비순환 그래프(Acyclic Graph)</h3>
<ul>
<li>순환(Cycle)을 가지고 있으면 순환 그래프</li>
<li>그렇지 않으면 비순환 그래프</li>
<li>n개의 정점이 사이클을 이루고 있을 경우 Cn이라고 표시
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/d59d9718-585c-445c-af1d-2aa51e555dc0/image.png" /></li>
</ul>
<h3 id="6-희소-그래프sparse-graph--밀집-그래프dense-graph">6. 희소 그래프(Sparse Graph) / 밀집 그래프(Dense Graph)</h3>
<ul>
<li>희소 그래프 : 노드 수보다 간선 수가 적은 그래프</li>
<li>밀집 그래프 : 노드 수보다 간선 수가 큰 그래프
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/2137b758-72d9-413a-a9af-d8f4f61415f5/image.png" /></li>
</ul>
<h3 id="7-단순-그래프simple-graph">7. 단순 그래프(Simple Graph)</h3>
<ul>
<li>중복된 간선과 loop가 없는 그래프</li>
<li>단순 그래프에서 정점의 개수가 n이라고 했을 때, 최대 n-1개의 간선을 가질 수 있다
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/8aa73936-1f77-48fd-9dd7-b35bd68b9f40/image.png" /></li>
</ul>
<h3 id="8-완전-그래프complete-graph">8. 완전 그래프(Complete Graph)</h3>
<ul>
<li>간선의 수가 최대인 그래프</li>
<li>정점의 수가 n개 이면 Kn으로 표시</li>
<li>정점의 수 : n , 간선의 수 : n*(n-1)/2
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/e6651176-e8b4-436c-ac72-cad1605e9ee3/image.png" /></li>
</ul>
<hr />
<h2 id="📊그래프의-구현">📊그래프의 구현</h2>
<h3 id="1-인접-행렬adjacent-matrix">1. 인접 행렬(Adjacent matrix)</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/c3e43deb-a7bd-4187-b139-24f943e85083/image.png" /></p>
<h4 id="무방향-그래프의-인접-행렬">무방향 그래프의 인접 행렬</h4>
<ul>
<li>정점들을 배열의 인덱스로 표현하고, 간선은 배열 내의 값으로, 두 정점이 연결되어 있다면 1, 연결되어 있지 않다면 0으로 표현</li>
<li>자기 자신으로 연결되는 self loop가 없으므로 배열의 대각선은 모두 0</li>
<li>A-&gt;B가 연결되어 있다면 B-&gt;A도 연결되어 있기 떄문에 무방향 그래프를 인접 행렬로 구현하면 대각선을 기준으로 대칭이 된다.</li>
<li><strong>인접 행렬의 공간 복잡도는 O(V²)이다. (V : 정점 수(노드의 개수))</strong></li>
</ul>
<h4 id="유방향-그래프의-인접-행렬">유방향 그래프의 인접 행렬</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/0b5ed0b6-fc2b-49de-a71d-071823da43a2/image.png" /></p>
<ul>
<li>유방향 그래프를 인접 행렬로 구현하면 대칭구조가 아니다.</li>
<li>유방향 그래프도 self-loop가 없으므로 대각선에는 0이 나오는건 동일</li>
</ul>
<hr />
<h3 id="2-인접-리스트adjacent-list">2. 인접 리스트(Adjacent list)</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/00a7fc8c-880f-4a51-aba5-695f3a536358/image.png" /></p>
<h4 id="무방향-그래프의-인접-리스트">무방향 그래프의 인접 리스트</h4>
<ul>
<li>각각의 정점을 head로 시작해서 인접한 노드들을 전부 연결리스트로 연결</li>
<li>A정점은 B와 D에 연결되어 있으므로 A-&gt;B-&gt;D로 표현</li>
<li>다른 정점도 동일</li>
<li><strong>인접 리스트의 공간 복잡도는 O(V + E)이다. (V : 정점 수(노드의 개수), E : 간선의 수)</strong></li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/b20883af-6e24-4f9d-a80d-fbe4be8da722/image.png" /></p>
<h4 id="유방향-그래프의-인접-리스트">유방향 그래프의 인접 리스트</h4>
<ul>
<li>방향 그래프도 동일</li>
</ul>
<hr />
<h2 id="☑️인접-행렬-vs-인접-리스트">☑️인접 행렬 VS 인접 리스트</h2>
<blockquote>
<p>🤔그래프를 인접 행렬과 인접 리스트로 구현하는 것중 어떤 것이 더 효율적일까?
정답은 &quot;<strong>상황에 따라 다르다.</strong>&quot; 이다.
그렇다면 어떤 상황에서는 행렬이 유리하고, 어떤 상황에서는 리스트가 유리한지 알아보자.</p>
</blockquote>
<h3 id="📌희소-그래프-vs-밀집-그래프">📌희소 그래프 vs 밀집 그래프</h3>
<p>그래프 표현 방식은 그래프가 희소 그래프인지, 밀집 그래프인지에 따라 선택한다.
위 그래프의 종류 설명 시, <strong>정점에 비해 간선의 수가 적은 그래프를 희소 그래프</strong>라 했는데, 예를 들어 그래프의 정점이 100개 인데, 간선이 3개만 있는 경우를 희소 그래프라고 한다.
이 때, 이 그래프를 인접행렬로 구현하게 된다면 <strong>인접 행렬의 시간복잡도는 O(V²)</strong>이므로 <strong>100*100의 행렬</strong>이 필요하고, 무방향 그래프라고 가정하면 그 큰 행렬 안에 1값은 단 6개만 있는 행렬이 구현될 것이다.(메모리 낭비가 심하다)
그러나 <strong>인접 리스트로 구현하면 공간 복잡도 O(V + E)</strong>로 인해 <strong>106개의 노드</strong>만 있으면 된다. 방향 그래프라면 103개면 구현이 완료된다.
따라서, <strong>인접 리스트는 희소 그래프를 표현하는데 적절한 방법이다.</strong></p>
<p>반면, <strong>정점에 비해 간선의 수가 많은 그래프를 밀집 그래프</strong>라고 한다. 만약 정점이 100개인데 간선이 200개 있는 그래프가 있다고 생각해보자. 간선이 많아 <code>E ≈ V²</code>가 되면 <strong>인접 리스트의 O(V + E)가 결국 O(V²)에 가까워져 두 표현의 비용이 비슷해진다.</strong> 또한 알고리즘의 특성상 간선 존재 확인을 매우 자주 수행해야 한다면 인접 행렬은 O(1)로 즉시 확인 할 수 있어 시간면에서 유리하다.
어떤 정점이 다른 정점과 연결되어 있는지 확인하고 싶을 때 배열은 인덱스를 이용하여 O(1)이면 충분하지만, 인접 리스트는 head부터 해당 노드를 찾을 때까지 탐색을 해야하므로 시간이 더 오래걸리게 된다. <strong>즉, 밀집 그래프는 인접 행렬로 구현하는 것이 더 효과적이다.</strong></p>
<table>
<thead>
<tr>
<th>항목</th>
<th>인접 행렬 (Adjacency Matrix)</th>
<th>인접 리스트 (Adjacency List)</th>
</tr>
</thead>
<tbody><tr>
<td><strong>공간 복잡도</strong></td>
<td><strong>O(V²)</strong></td>
<td><strong>O(V+E)</strong></td>
</tr>
<tr>
<td><strong>정점의 인접 노드 탐색</strong></td>
<td><strong>O(1)</strong></td>
<td>*<em>O(n), n : 연결된 노드 개수 *</em></td>
</tr>
<tr>
<td><strong>구현에 유리한 그래프</strong></td>
<td><strong>밀집 그래프</strong></td>
<td><strong>희소 그래프</strong></td>
</tr>
</tbody></table>