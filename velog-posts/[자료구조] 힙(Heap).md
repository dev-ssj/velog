<h1 id="💡힙heap이란">💡힙(Heap)이란?</h1>
<blockquote>
<p><strong>완전 이진 트리(Complete Binary Tree)를 기초로 하는 자료구조</strong>로써, 여러 개의 값중에서 가장 크거나 작은 값을 빠르게 찾기 위해 만든 이진트리이다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/75a5d7c5-98c0-42d5-a899-9450b265ef83/image.png" /></p>
<hr />
<h2 id="✅힙의-특징">✅힙의 특징</h2>
<ol>
<li>힙은 <strong>최대힙(Max heap)</strong>과 <strong>최소힙(Min Heap)</strong>으로 나누어진다.
최대힙은 부모노드의 값이 자식노드들의 값보다 항상 크고, 최소 힙은 부모노드의 값이 자식노드의 값보다 항상 작다.</li>
<li>위 성질때문에 항상 <strong>느슨한 정렬상태(반정렬 상태)</strong>를 유지한다.</li>
<li>힙은 <strong>중복값을 허용</strong>한다.</li>
<li>보통 배열로 구현하며 인덱스로 부모/자식 위치를 계산한다.</li>
</ol>
<hr />
<h2 id="✅힙을-배열로-구현하기">✅힙을 배열로 구현하기</h2>
<blockquote>
<p>힙은 대체적으로 배열로 구현하며, 완전이진트리를 기본으로 하기 때문에 배열로 구현하기에 용이하다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/89911bc4-1dd2-45f8-a236-8c9a8598f830/image.png" /></p>
<ul>
<li>루트 노드는 배열의 0번 인덱스에 저장</li>
<li>각 노드를 기점으로 <strong>왼쪽 자식 노드는 a[i*2+1]</strong>, <strong>오른쪽 자식 노드는 a[i*2+2]</strong>의 인덱스에 저장된다.</li>
<li>특정 인덱스의 노드에서 <strong>부모 노드는 a[(i−1)//2]</strong>로 찾을 수 있다.</li>
</ul>
<hr />
<h2 id="✅힙의-데이터-처리삽입삭제">✅힙의 데이터 처리(삽입/삭제)</h2>
<p>최대힙의 부모노드는 항상 자식노드의 값보다 크고, 최소힙의 부모노드는 항상 자식노드의 값보다 작다는 조건을 가지고 있다. <br />하지만 힙에서 <strong>삽입 또는 삭제</strong>가 일어나게 된다면 힙의 조건이 깨질 수 있다. 이러한 경우에 <strong>힙의 조건을 만족할 수 있게 노드들의 위치를 바꿔가며 힙의 재구조화(heapify)를 해주어야 한다.</strong>
삽입/삭제의 경우 모두 연산 자체는 O(1)이지만, heapify의 과정을 거치기 때문에 <strong>O(log n)의 시간 복잡도</strong>를 가지게 된다.</p>
<h2 id="✅데이터-삽입">✅데이터 삽입</h2>
<blockquote>
<ol>
<li>새로운 노드는 리프노드가 아니면서 가장 말단에 있는 노드의 자식 노드로 추가</li>
<li>그 노드와 부모 노드를 비교</li>
<li>규칙에 맞으면 그대로 두고, 아니라면 부모 자리와 교환</li>
<li>규칙에 맞을때까지 반복</li>
</ol>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/b5d0ee3e-0ceb-42fa-a379-0f993897fbe8/image.png" /></p>
<h2 id="✅데이터-삭제">✅데이터 삭제</h2>
<blockquote>
<p>📌힙의 삭제는 <strong>최댓값 혹은 최솟값이 저장된 루트 노드만 제거</strong>할 수 있다.</p>
</blockquote>
<ol>
<li>루트 노드를 제거하고, 마지막 원소를 루트로 이동시킨다.</li>
<li>루트로 올라간 노드와 그 자식 노드들을 비교한다.</li>
<li>조건에 만족하면 그대로 두고, 그렇지 않다면 자식과 교환</li>
</ol>
<p>-** 최대힙**
    1. 부모보다 더 큰 자식이 없으면 교환하지 않고 끝낸다.
    2. 부모보다 더 큰 자식이 하나만 있으면 그 자식하고 교환하면 된다.
    3. 부모보다 더 큰 자식이 둘 있으면 자식들 중 큰 값과 교환한다.</p>
<ul>
<li><strong>최소힙</strong><ol>
<li>부모보다 더 작은 자식이 없으면 교환하지 않고 끝낸다.</li>
<li>부모보다 더 작은 자식이 하나만 있으면 그 자식하고 교환하면 된다.<ol start="3">
<li>부모보다 더 작은 자식이 둘 있으면 자식들 중 작은 값과 교환한다.</li>
</ol>
</li>
</ol>
</li>
</ul>
<ol start="5">
<li>조건을 만족할 때까지 3을 반복</li>
</ol>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/2449e7a3-459b-4e23-baa8-e7c833cafcb8/image.png" /></p>
<hr />
<h1 id="💡힙-정렬heap-sort">💡힙 정렬(Heap Sort)</h1>
<blockquote>
<p>배열 전체를 힙으로 만든 뒤 루트부터 하나씩 꺼내 정렬하는 방식</p>
</blockquote>
<ul>
<li>힙 정렬의 <strong>시간 복잡도는 O(n log n)</strong>으로 빠른 정렬에 속한다.</li>
<li>최악의 경우에 O(N2)의 성능을 내는 퀵 정렬과 달리 <strong>항상 O(n log n)정렬의 성능</strong>을 발휘한다.</li>
<li>추가적인 배열을 사용하지 않아 메모리 측면에서도 이점을 보인다.</li>
</ul>
<h2 id="✅힙-정렬의-과정">✅힙 정렬의 과정</h2>
<ul>
<li>최대 힙을 구현한 뒤, 루트 노드를 삭제하여 배열의 맨 마지막에 넣어주고, 깨진 힙을 재구조화하는 과정을 반복한다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/e22eb0d3-7857-44d0-9e95-68a0c65a0efd/image.png" />
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/7d268d07-a785-425f-b9fa-11e131875c10/image.png" /></li>
</ul>