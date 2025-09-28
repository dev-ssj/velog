<h1 id="🤔가비지-컬렉션garbage-collection-gc이란">🤔가비지 컬렉션(Garbage Collection, GC)이란?</h1>
<p><strong>가비지 컬렉션</strong>은 자바의 메모리 관리 방법 중 하나로, <strong>JVM의 Heap 영역에서 동적으로 할당했던 메모리 중 필요 없게 된(사용하지 않는) 메모리 객체를 모아 주기적으로 제거하는 프로세스</strong>를 뜻한다.</p>
<p>C/C++은 이러한 가비지 컬렉션이 없어 프로그래머가 수동으로 메모리 할당과 해제를 일일이 해줘야 한다. Java는 개발자 입장에서 메모리 관리와 메모리 누수 문제에 대해 신경쓰지 않아도 되어 좀더 수월하게 개발 할 수 있다.</p>
<p>하지만, 이런 가비지 컬렉션에도 단점이 있는데, 이런 단점을 <span style="color: red;"><strong>Stop-The-World</strong></span>라고 한다.
자동으로 처리해준다 해도 <strong>메모리가 언제 해제되는지 정확하게 알수 없어 제어하기 힘들며, 가비지 컬렉션이 동작하는 동안에는 다른 동작을 멈추기 때문에 오버헤드가 발생되는 문제점</strong>이 있다.
이로 인해 GC가 너무 자주 실행되면 소프트웨어 성능 하락의 문제가 되기도 한다.</p>
<hr />
<h2 id="✅가비지-컬렉션의-대상">✅가비지 컬렉션의 대상</h2>
<p>일반적으로 아래와 같은 경우에 GC의 대상이 된다.</p>
<ol>
<li>객체가 NULL인 경우 (String str = null)</li>
<li>블럭 실행 종료 후, 블럭 안에서 생성된 객체</li>
<li>부모 객체가 NULL인 경우, 포함하는 자식 객체
GC는 <code>Weak Generational Hypothesis</code> 에 기반하는데, 이게 무엇인지 알아보기 전에 GC의 메모리 해제 과정에 대해 살펴보자.</li>
</ol>
<hr />
<h2 id="✅gc의-메모리-해제-과정">✅GC의 메모리 해제 과정</h2>
<p>GC가 메모리를 해제할 때는 <strong>Mark And Sweep</strong>이란 알고리즘을 사용한다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/01667cd5-df7d-4140-8430-66aae1b94d8d/image.png" />
가비지 컬렉션은 대상 객체를 <span style="color: red;"><strong>식별(Mark)</strong></span>하고, <span style="color: red;"><strong>제거(Sweep)</strong></span>하며 객체가 제거되어 파편화된 <span style="color: red;"><strong>메모리 영역을 앞에서부터 채워나가는 작업(Compaction)</strong></span>을 수행하게 된다.</p>
<h4 id="1️⃣-mark">1️⃣ Mark</h4>
<ul>
<li><strong>Root Space</strong>로부터 그래프 순회를 통해 연결된 객체들을 찾아내어 어떤 객체를 참조하고 있는지 찾아서 마킹한다.</li>
<li>참조하고 있는 객체가 없는 객체가 바로 GC의 대상인 사용하지 않는 객체이다.</li>
</ul>
<h4 id="2️⃣sweep">2️⃣Sweep</h4>
<ul>
<li>Mark 과정에서 찾은 참조하고 있지 않은 객체들 즉, <strong>사용하지 않는 객체를 Heap에서 제거</strong>한다.</li>
</ul>
<h4 id="3️⃣compact">3️⃣Compact</h4>
<ul>
<li>Sweep 후에 남아있는 객체(사용중인 객체들)을 Heap의 시작 주소로 모아 묶는다.</li>
<li>이들을 묶음으로써 공간이 생기므로 새로운 메모리 할당 시에 더 쉽고 빠르게 진행이 가능하다.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/370834ef-2677-49d4-808c-12e91a5efa00/image.gif" /></p>
<hr />
<h2 id="✅weak-generational-hypothesis">✅Weak Generational Hypothesis</h2>
<p>JVM의 Heap영역은 처음 설계될 때 다음과 같은 2가지 전제로 설계되었다.</p>
<ul>
<li>대부분의 객체는 금방 접근 불가능한 상태(Unreachable)가 된다.</li>
<li>오래된 객체에서 새로운 객체로의 참조는 매우 적게 존재한다.</li>
</ul>
<p>즉, <strong>객체는 대부분 일회성이며, 메모리에 오랫동안 남아있는 경우는 드물다는 것</strong>이다.</p>
<p>이 가설에 기반하여 Java는 객체의 생존기간에 따라 물리적인 Heap영역을 <strong>Young영역</strong>과 <strong>Old 영역</strong>으로 나누게 되었다. <strong>신규로 생성되는 객체는 Young 영역</strong>에, <strong>오랫동안 살아남은 객체는 Old 영역</strong>에 보관한다.</p>
<hr />
<h1 id="💡generational-garbage-collection">💡Generational Garbage Collection</h1>
<h2 id="✅generational-garbage-collection의-구조">✅Generational Garbage Collection의 구조</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/ef348aeb-f663-414a-98c2-306a09d21a4b/image.png" /></p>
<h3 id="young-영역">Young 영역</h3>
<ul>
<li><strong>새롭게 생성되는 객체가 할당되는 영역</strong></li>
<li>대부분의 객체가 금방 Unreachable상태가 되기 때문에 많은 객체가 Young 영역에 생성되었다가 사라진다.</li>
<li>Young 영역에 대한 가비지 컬렉션을 <span style="color: red;"><strong>Minor GC</strong></span>라고 부른다.</li>
</ul>
<h3 id="old-영역">Old 영역</h3>
<ul>
<li><strong>Young 영역에서 살아남은 객체가 복사되는 영역</strong></li>
<li>Young 영역보다 크게 할당되며, 영역의 크기가 큰 만큼 가비지는 적게 발생한다.</li>
<li>Old 영역에 대한 가비지 컬렉션을 <span style="color: red;"><strong>Major GC 또는 Full GC</strong></span>라고 부른다.</li>
</ul>
<blockquote>
<p>또 다시 힙 영역은 더욱 효율적인 GC를 위해 Young 영역을 3가지 영역으로 나눈다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/9b1d0c78-2040-40e8-a908-5cc967c2adb5/image.png" /></p>
<h3 id="eden">Eden</h3>
<ul>
<li><strong>new를 통해 새로 생성된 객체</strong>가 위치</li>
<li>장기적인 쓰레기 수집 후 살아남은 객체들은 Survivor영역으로 보냄</li>
</ul>
<h3 id="survivor-0--survivor-1">Survivor 0 / Survivor 1</h3>
<ul>
<li><strong>최소 1번의 GC 이상 살아남은 객체가 존재하는 영역</strong></li>
<li>Survivor 0 또는 Survivor 1 둘중 하나는 꼭 비어 있어야 한다는 규칙이 존재.</li>
</ul>
<hr />
<h2 id="✅minor-gc-과정">✅Minor GC 과정</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/6dca5320-d748-43bc-aa99-073cd54e0649/image.png" /></p>
<ul>
<li><strong>Yong Generaion 영역은 짧게 살아남는 메모리들이 존재하는 공간</strong>이다.</li>
<li>모든 객체는 처음에 Young Generation에 생성된다.</li>
<li>Old Generation에 비해 공간이 상대적으로 작기 때문에 메모리 상의 객체를 찾아 제거하는데 적은 시간이 걸린다.</li>
</ul>
<h3 id="1-처음-생성된-객체는-young-generation-영역의-일부인-eden-영역에-위치">1. 처음 생성된 객체는 Young Generation 영역의 일부인 Eden 영역에 위치</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/8fd82b8a-d832-409e-a40c-b577dc85b21b/image.png" /></p>
<h3 id="2-객체가-계속-생성되어-eden-영역이-꽉차게-되면-minor-gc가-발생">2. 객체가 계속 생성되어 Eden 영역이 꽉차게 되면 Minor GC가 발생</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/13ab9ef3-5237-4e6a-b61c-9e7c7ebf57c8/image.png" /></p>
<h3 id="3-mark-동작을-통해-참조되는-객체-탐색">3. Mark 동작을 통해 참조되는 객체 탐색</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/830d771c-22dc-496a-80db-788938416b29/image.png" /></p>
<h3 id="4-eden-영역에서-살아남은-객체는-survivor-영역으로-이동">4. Eden 영역에서 살아남은 객체는 Survivor 영역으로 이동</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/52ca34ad-c8a8-4fe3-8829-2f69a9cfd101/image.png" /></p>
<h3 id="5-eden영역에서-사용되지-않은-객체의-메모리를-해제sweep">5. Eden영역에서 사용되지 않은 객체의 메모리를 해제(sweep)</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/2640cbd3-7196-4d39-a576-227ca21d5b49/image.png" /></p>
<h3 id="6-살아남은-모든-객체들의-age-값이-1씩-증가">6. 살아남은 모든 객체들의 age 값이 1씩 증가</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/99956102-6a1c-413b-bbc5-c720bfc3fbc1/image.png" /></p>
<h3 id="7-또다시-eden-영역에-신규-객체들로-가득차게-되면-다시-minor-gc가-발생되고-mark-실행">7. 또다시 Eden 영역에 신규 객체들로 가득차게 되면 다시 Minor GC가 발생되고 mark 실행</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/36767861-4641-4e33-8fe9-e1de3de8b92b/image.png" /></p>
<h3 id="8-marking한-객체들을-비어있는-survivor-1다른-survivor-영역으로-이동하고-sweep">8. marking한 객체들을 비어있는 Survivor 1(다른 Survivor 영역)으로 이동하고 sweep</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/8d2bf256-a410-4fd8-9369-7e1abbd5f964/image.png" /></p>
<h3 id="9-다시-살아남은-모든-객체들의-age-1씩-증가">9. 다시 살아남은 모든 객체들의 age 1씩 증가</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/8d285772-bd59-4345-af47-eb3f86818c7f/image.png" /></p>
<h3 id="10-이-모든-과정을-반복">10. 이 모든 과정을 반복</h3>
<hr />
<h2 id="✅major-gc-과정">✅Major GC 과정</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/227258a9-480b-4e90-b286-2b41333bf6c0/image.png" /></p>
<ul>
<li><strong>Old Generation은 길게 살아남는 메모리들이 존재하는 공간</strong></li>
<li>Old Generation의 객체들은 거슬러 올라가면 처음에는 Young Generation에 의해 시작되었으나, GC 과정 중에 제거되지 않은 경우 age 임계값이 차게되어 이동된 객체들이다.</li>
<li>Major GC는 객체들이 계속 Promotion되어 <strong>Old 영역의 메모리가 부족해지면 발생</strong>하게 된다.</li>
</ul>
<h3 id="1-객체의-age가-임계값예시는-8에-도달하게-되면">1. 객체의 age가 임계값(예시는 8)에 도달하게 되면</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/7496c072-df55-4c9a-8852-7cef12ec7713/image.png" /></p>
<h3 id="2-이-객체들은-old-generation으로-이동된다promotion">2. 이 객체들은 Old Generation으로 이동된다.(promotion)</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/1c13c66e-7a1e-4797-b77b-1125bd70cac3/image.png" /></p>
<h3 id="3-위-과정이-반복되어-old-generaion-영역이-가득-차게되면-major-gc가-발생한다">3. 위 과정이 반복되어 Old Generaion 영역이 가득 차게되면 Major GC가 발생한다.</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/b7ec9cc2-fb98-4887-b718-2b63a05645b1/image.png" /></p>
<p>Major GC는 Old Generation 영역이 가득 찼을 때 실행된다. 이때 GC는 Old Generation에 존재하는 객체들을 검사해 <strong>참조되지 않는 객체들을 한꺼번에 정리</strong>한다.</p>
<p>Old Generation은 Young Generation보다 공간이 훨씬 크기 때문에, Major GC가 수행될 때는 더 많은 객체를 스캔하고 해제해야 한다. 그 결과 GC 처리 시간이 길어질 수 있다.</p>
<p>이때, <span style="color: red;"><strong>Stop-The-World</strong></span> 문제가 발생하게 된다.
Major GC가 실행되는 동안에는 애플리케이션 스레드가 일시 중지되며, GC 작업이 완료될 때까지 모든 요청 처리가 멈추게 되는데, 이로 인해 지연이나 성능 저하가 발생할 수 있으며, 이를 흔히 <strong>GC 오버헤드 문제</strong>라고 부른다.</p>
<p>따라서 이런 문제를 해결하고 최적화하기 위해 다양한 가비지 컬렉션 알고리즘이 개발되었다.</p>
<hr />
<h2 id="가비지-컬렉션-알고리즘-종류">가비지 컬렉션 알고리즘 종류</h2>
<h3 id="serial-gc">Serial GC</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/1cc6ff2e-f3fb-412f-9206-57ec72090adc/image.png" /></p>
<ul>
<li>서버의 CPU 코어가 1개 일때 사용하기 위해 개발된 가장 단순한 GC</li>
<li>GC를 처리하는 스레드가 1개여서 가장 Stop-The-World의 시간이 가장 길다</li>
<li>**보통 실무에서는 사용하지 않는다.</li>
<li>*</li>
</ul>
<hr />
<h3 id="parallel-gc">Parallel GC</h3>
<ul>
<li>Java 8의 디폴트 GC</li>
<li>Serial GC와 기본적인 알고리즘은 같지만, Young 영역의 Minor GC를 멀티쓰레드로 수행(Old 영역은 여전히 싱글 스레드)
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/541c5042-1da1-4d53-8d62-3cec1f43c595/image.png" /></li>
</ul>
<hr />
<h3 id="parallel-old-gc">Parallel Old GC</h3>
<ul>
<li>Parallel GC를 개선한 버전</li>
<li>Young 영역 뿐만 아니라 Old 영역에서도 멀티스레드로 GC 수행</li>
<li>새로운 가비지 컬렉션 청소 방식인 Mark-Summary-Compact 방식을 이용</li>
</ul>
<hr />
<h3 id="cms-gc">CMS GC</h3>
<ul>
<li>어플리케이션의 스레드와 GC 스레드가 동시에 실행되어 Stop-The-World 시간을 최대한 줄이기 위해 고안된 GC</li>
<li>GC 과정이 매우 복잡하며 메모리 파편화 문제 존재</li>
<li>GC 대상을 파악하는 과정이 복잡한 여러단계로 수행되기 때문에 다른 GC 대비 CPU 사용량이 높음</li>
<li><strong>Java14에서부터 사용이 중지</strong></li>
</ul>
<hr />
<h3 id="g1-gc">G1 GC</h3>
<ul>
<li>CMS GC를 대체하기 위해 나온 GC</li>
<li>Java 9+ 버전의 디폴트 GC로 지정</li>
<li>기존의 GC 알고리즘에서는 Heap 영역을 물리적으로 고정된 Young / Old 영역으로 나누어 사용하였지만, G1 gc는 아예 이러한 개념을 뒤엎는 Region이라는 개념을 새로 도입하여 사용.전체 Heap 영역을 Region이라는 영역으로 체스같이 분할하여 상황에 따라 Eden, Survivor, Old 등 역할을 고정이 아닌 동적으로 부여
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/20feb1f9-17ea-40af-8179-4d3ca59067a0/image.png" /></li>
</ul>
<hr />
<h3 id="shenandoah-gc">Shenandoah GC</h3>
<ul>
<li>Java 12에 release</li>
<li>레드햇에서 개발한 GC</li>
<li>기존 CMS가 가진 단편화, G1이 가진 pause의 이슈를 해결</li>
<li>강력한 Concurrency와 가벼운 GC 로직으로 heap 사이즈에 영향을 받지 않고 일정한 pause 시간이 소요가 특징
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/2f36f585-a5e0-4145-91ca-b27796df2aa5/image.png" /></li>
</ul>
<hr />
<h3 id="zgc">ZGC</h3>
<ul>
<li>Java 15에 release</li>
<li>G1의 Region 처럼,  ZGC는 ZPage라는 영역을 사용하며, G1의 Region은 크기가 고정인데 비해, ZPage는 2mb 배수로 동적으로 운영됨. (큰 객체가 들어오면 2^ 로 영역을 구성해서 처리)</li>
<li>ZGC가 내세우는 최대 장점 중 하나는 힙 크기가 증가하더도 <strong>'stop-the-world'의 시간이 절대 10ms를 넘지 않는다는 것</strong>
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/0546894e-bf51-4ff1-8027-c0dc3e835cde/image.png" /></li>
</ul>