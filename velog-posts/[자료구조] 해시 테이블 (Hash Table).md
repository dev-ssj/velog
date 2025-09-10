<h1 id="💡해시테이블hash-table이란">💡해시테이블(Hash Table)이란?</h1>
<blockquote>
<p>해시테이블은 <strong>키(Key) → 값(Value)를 빠르게 찾기 위해서 해시 함수를 사용하는 자료구조</strong> 이다.
해시 테이블은 데이터를 보관할 때 <strong>주소를 계산해서 바로 꺼내오는 방식</strong>을 이용한다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/f8558b76-b1db-482a-9a5c-8053c4120237/image.png" /></p>
<ul>
<li>위 <code>John Smith</code>라는 사람의 전화번호를 찾는다는 가정을 해보자.</li>
<li>해시 함수의 입력값은 John Smith고, 해시 함수를 통해 얻은 인덱스는 <code>&quot;01&quot;</code>이다.</li>
<li>위에서 얻은 값을 배열의 인덱스로 사용하여 인덱스가 01인 bucket에서 <code>&quot;521-8976&quot;</code>을 찾는다.</li>
</ul>
<hr />
<h2 id="💭해싱-원리">💭해싱 원리</h2>
<h3 id="1️⃣key-값을-입력받는다">1️⃣Key 값을 입력받는다.</h3>
<ul>
<li>예시 : apple<h3 id="2️⃣해시-함수를-통과시켜-숫자-인덱스를-얻는다">2️⃣해시 함수를 통과시켜 숫자 인덱스를 얻는다.</h3>
</li>
<li>hash(&quot;apple&quot;) → 37</li>
</ul>
<h3 id="3️⃣그-숫자를-배열의-인덱스로-사용해-값을-저장한다">3️⃣그 숫자를 배열의 인덱스로 사용해 값을 저장한다.</h3>
<ul>
<li>table[37] → &quot;사과&quot;</li>
</ul>
<h3 id="4️⃣다시-apple을-찾을-때도-똑같이-해시-함수를-거쳐-같은-인덱스를-찾아간다">4️⃣다시 &quot;apple&quot;을 찾을 때도 똑같이 해시 함수를 거쳐 같은 인덱스를 찾아간다.</h3>
<blockquote>
<p>👉<strong>결국 &quot;키 → 해시 함수 → 배열 인덱스&quot; 를 거쳐 해싱한다.</strong></p>
</blockquote>
<hr />
<h1 id="⚡해시-충돌-hash-collsion">⚡해시 충돌 (Hash Collsion)</h1>
<blockquote>
<p>해시테이블은 데이터를 Key로 간소화하여 저장한다. 하지만 만약 여러 데이터가 같은 해시를 갖는다면 어떻게 될까? 
이러한 상황을 <strong>해시 충돌(Hash Collsion)</strong>이라고 한다.
같은 해시값을 갖는 데이터들이 생긴다는 것은 특정 해시에 데이터가 집중된다는 소리이며, <strong>너무 많은 해시 충돌은 해시 테이블의 성능을 떨어 뜨린다.</strong>
이제 이러한 해시 충돌을 해결하기 위한 방법을 알아보자.</p>
</blockquote>
<h2 id="1️⃣체이닝chaining">1️⃣체이닝(Chaining)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/164214c7-4f6a-430a-81e7-d996989f3afd/image.png" /></p>
<blockquote>
<p>해시테이블의 기본 방식인 체이닝은 충돌 발생 시, <strong>같은 인덱스에 연결 리스트나 트리를 두어 여러 값을 저장하는 방식</strong>이다.</p>
</blockquote>
<ul>
<li>충돌이 발생한 윤아, 서현은 윤아의 다음 아이템인 서현이 서로 연결 리스트로 연결되었다. </li>
<li><code>table[2] =[&quot;윤아&quot;,&quot;서현&quot;]</code><h3 id="⭕체이닝의-장점">⭕체이닝의 장점</h3>
</li>
<li>구현이 단순(버킷에 연결리스트/트리만 붙이면 끝)</li>
<li>Load Factor(저장된 원소 수/버킷 수)가 커져도, 테이블 크기를 반드시 늘리지 않아도 됨</li>
<li>삭제가 쉽다.</li>
<li>확장 시 충돌 관리에 유연</li>
</ul>
<h3 id="❌체이닝의-단점">❌체이닝의 단점</h3>
<ul>
<li>포인터나 리스트/트리 때문에 메모리 추가 소모</li>
<li>캐시 효율이 떨어짐 -&gt; 메모리가 여기저기 흩어져 있음</li>
</ul>
<hr />
<h2 id="2️⃣오픈-어드레싱open-addressing">2️⃣오픈 어드레싱(Open Addressing)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/8c8c06cb-620c-472a-b17f-dc8203953413/image.png" /></p>
<blockquote>
<p>오픈 어드레싱 방식은 충<strong>돌 발생 시 버켓을 탐사하여 빈 공간을 찾고, 그 공간에 데이터를 삽입하는 방식</strong>을 말한다. 오픈 어드레싱 방식은 아래와 같다.</p>
</blockquote>
<ul>
<li><strong>선형 탐사</strong> : 충돌나면 다음 칸 확인</li>
<li><strong>이차 탐사</strong> : 1칸, 4칸, 9칸 ... 떨어진 곳 탐색</li>
<li><strong>더블 해싱</strong> : 다른 해시 함수를 한번 더 적용</li>
</ul>
<h3 id="⭕오픈-어드레싱의-장점">⭕오픈 어드레싱의 장점</h3>
<ul>
<li>추가 자료구조가 필요 없음(배열 하나로 해결)</li>
<li>캐시 친화적 → 탐사 시 메모리 접근이 순차적이라 비교적 바름<h3 id="❌오픈-어드레싱의-단점">❌오픈 어드레싱의 단점</h3>
</li>
<li>Load Factor가 커지면 성능이 급격히 나빠짐</li>
<li>삭제가 복잡</li>
<li>클러스터링 문제 발생(충돌이 몰리면 성능이 더 나빠진다)</li>
</ul>
<hr />
<h3 id="☑️그럼-어떤걸-써야하나">☑️그럼 어떤걸 써야하나?</h3>
<blockquote>
<p><strong>👉메모리 여유가 있고, 해시 함수 충돌 가능성이 꽤 있는 경우 → 체이닝 선호
👉메모리 제한이 있고, 해시 함수가 균등 분포를 잘 보장하는 경우 → 오픈 어드레싱 선호</strong></p>
</blockquote>
<hr />
<h2 id="⏱️시간-복잡도">⏱️시간 복잡도</h2>
<blockquote>
<ul>
<li><strong>평균 : O(1)</strong> → 해시 함수가 잘 설계되고, 충돌이 적을 때</li>
</ul>
</blockquote>
<ul>
<li>** 최악 : O(n)** → 모든 값이 한 버킷에 몰리면 연결 리스트 탐색 수준까지 느려질 수 있다.
👉 <strong>좋은 해시 함수</strong>가 매우 중요!</li>
</ul>
<hr />
<h2 id="👍좋은-해시-함수의-조건">👍좋은 해시 함수의 조건</h2>
<h4 id="1️⃣균등성uniformity--값이-테이블-전체에-고르게-분포되어야-한다">1️⃣균등성(Uniformity) : 값이 테이블 전체에 고르게 분포되어야 한다.</h4>
<h4 id="2️⃣결정성determinism--같은-입력은-항상-같은-해시값">2️⃣결정성(Determinism) : 같은 입력은 항상 같은 해시값.</h4>
<h4 id="3️⃣효율성-efficiency--빠르게-계산-가능해야-함">3️⃣효율성 (Efficiency) : 빠르게 계산 가능해야 함.</h4>
<h4 id="4️⃣낮은-충돌률-low-collision--다른-키가-같은-해시값을-덜-가지게">4️⃣낮은 충돌률 (Low Collision) : 다른 키가 같은 해시값을 덜 가지게.</h4>
<hr />
<h2 id="✅실제-사용-사례">✅실제 사용 사례</h2>
<h3 id="1-세션-저장">1. 세션 저장</h3>
<ul>
<li>사용자 로그인 시 <code>sessionId</code> → <code>sessionData</code> 매핑</li>
<li>세션 조회 시 <strong>O(1)에 가까운 속도로 탐색 가능</strong></li>
</ul>
<h3 id="2-캐시cache">2. 캐시(Cache)</h3>
<ul>
<li><code>url → response</code>, <code>query → result</code> 같은 매핑.</li>
<li>Redis 같은 인메모리 DB는 <strong>내부적으로 해시 테이블을 활용</strong></li>
</ul>
<h3 id="3-로드-밸런싱-consistent-hashing">3. 로드 밸런싱 (Consistent Hashing)</h3>
<ul>
<li>분산 서버에 요청을 나눌 때, <strong>키를 해시값으로 바꿔 특정 서버에 매핑</strong></li>
<li>서버 추가/삭제 시에도 최소한의 재배치만 발생</li>
</ul>