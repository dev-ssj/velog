<blockquote>
<p>이전에 JVM, JRE, JDK의 개념에 대해서 알아보는 시간을 가졌다.이번엔  좀 더 자세히 JVM의 구조에 대해서 알아보도록 하자. 
<a href="https://velog.io/@dev_ssj/JAVA-JDK-JRE-JVM%EC%9D%98-%EA%B0%9C%EB%85%90-%EC%B0%A8%EC%9D%B4">JDK, JRE, JVM의 개념</a></p>
</blockquote>
<h1 id="🧐jvm이-뭐였지">🧐JVM이 뭐였지?</h1>
<p>JVM은 자바 가상 머신으로, <strong>자바 바이트 코드를 각 운영체제에 맞는 기계로 바꾸어 실행하는 가상머신</strong>이다. 즉, 자바 바이트 코드(.class)를 해석하여 실행 시켜주는 머신인데, 대체 어떤 방법으로 자바 바이트 코드를 해석하고, 프로그램 실행까지 시키는지 알아보도록 하자.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/9cc3d8ef-9531-4f4b-bbad-456795f621b8/image.png" /></p>
<hr />
<h1 id="✅jvm의-구성요소">✅JVM의 구성요소</h1>
<p><strong>JVM은 크게 5가지의 요소로 나누어진다.</strong>
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/2523a519-fa8f-4647-bd20-752e82aea741/image.png" /></p>
<h4 id="1-클래스-로더-class-loader">1. 클래스 로더 (Class Loader)</h4>
<h4 id="2-런타임-데이터-영역-runtime-data-area">2. 런타임 데이터 영역 (Runtime Data Area)</h4>
<h4 id="3-실행-엔진-execution-engine">3. 실행 엔진 (Execution Engine)</h4>
<h4 id="4-jni---네이티브-인터페이스-java-native-interface">4. JNI - 네이티브 인터페이스 (Java Native Interface)</h4>
<h4 id="5-네이티브-메서드-라이브러리-native-method-libraries">5. 네이티브 메서드 라이브러리 (Native Method Libraries)</h4>
<hr />
<h2 id="1️⃣클래스-로더class-loader">1️⃣클래스 로더(Class Loader)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/f096e647-5828-446f-b21a-2f47a76de656/image.png" />
<strong>클래스 로더</strong>는 JVM 안에서 <strong>클래스 파일(.class)을 동적으로 로드</strong>하고, J<strong>VM의 메모리 영역인 Runtime Data Area에 메모리를 할당하여 실행 가능한 상태로 만든다.</strong> 프로그램 실행 시 필요한 클래스만 동적으로 적재하여 메모리 사용을 최적화한다.
더 자세히 말하자면, 클래스 로더는 클래스의 로드(Load), 연결(Link), 초기화(Intialization) 과정을 통해 실행을 준비하게 된다.
즉, <strong>클래스 로더 클래스 파일을 가져와 메모리에 로드하고, 클래스에 문제가 없는지 검증하고 변수를 적절한 값으로 초기화 하는 과정을 담당한다.</strong></p>
<hr />
<h2 id="2️⃣런타임-데이터-영역runtime-data-area">2️⃣런타임 데이터 영역(Runtime Data Area)</h2>
<p>런타임 데이터 영역은 JVM이 실행하는 동안 데이터를 저장하는 메모리 영역이다.</p>
<h3 id="1-메서드-영역">1) 메서드 영역</h3>
<ul>
<li><strong>바이트 코드를 처음 메모리 공간에 올릴 때 초기화되는 대상을 저장하기 위한 공간</strong></li>
<li>정적 필드와 클래스 구조만을 가지고 있다.</li>
<li>JVM 메모리의 정적 영역으로, 모든 스레드가 공유하는 메모리 공간</li>
<li>JVM이 동작하고 <strong>클래스가 로드될 때 적재되어 프로그램이 종료될 때까지 저장</strong>됨</li>
</ul>
<h3 id="2-힙-영역">2) 힙 영역</h3>
<ul>
<li><strong>new 연산자로 생성되는 클래스, 인스턴스 변수, 배열 등 Reference Type이 저장되는 공간</strong></li>
<li><strong>가비지 컬렉터가 관리</strong></li>
<li>모든 스레드가 공유하는 영역</li>
</ul>
<h3 id="3-스택-영역">3) 스택 영역</h3>
<ul>
<li><strong>int, long, boolean 등 기본 자료형 생성 시 저장하는 공간</strong></li>
<li>각 스레드마다 독립적으로 할당되는 공간</li>
<li>메서드 호출시마다 프레임이 생성되어 지역 변수, 매개 변수 등이 저장됨</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/bb2005f8-781b-4f2c-8273-f2451cc75463/image.png" /></p>
<h3 id="4-pcprogram-counter-레지스터">4) PC(Program Counter) 레지스터</h3>
<ul>
<li>각 스레드 별로 하나씩 존재</li>
<li><strong>현재 실행중인 JVM 명령의 주소를 저장하는 공간</strong></li>
</ul>
<h3 id="5-네이티브-메서드-스택">5) 네이티브 메서드 스택</h3>
<ul>
<li><strong>실제 실행할 수 있는 기계어로 작성된 프로그램을 실행 시키는 영역</strong></li>
<li>자바 의외의 네이티브 코드(C/C++) 실행 시 사용되는 메모리 영역이기도 함</li>
</ul>
<hr />
<h2 id="3️⃣실행-엔진execution-engine">3️⃣실행 엔진(Execution Engine)</h2>
<p>실행 엔진은 클래스 로더를 통해 런타임 데이터 영역에 배치된 바이트 코드를 명령어 단위로 읽어서 실행한다. 자바 바이트 코드(.class)는 가상 머신이 이해할 수 있는 중간 레벨로 컴파일된 코드이다. 그래서 <strong>실행 엔진은 바이트 코드를 JVM내부에서 실제로 기계가 실행할 수 있는 형태로 바꿔준다.</strong>
이 때, 바이트 코드를 실행 시키는 방법은 크게 두가지로 분류 할 수 있다.</p>
<h3 id="1-인터프리터interpreter">1) 인터프리터(Interpreter)</h3>
<ul>
<li><strong>바이트 코드 명령어를 하나씩 읽어서 해석하고 바로 실행</strong></li>
<li>JVM안에서 바이트 코드는 기본적으로 인터프리터 방식으로 동작</li>
<li>같은 메소드라도 여러번 호출이 된다면 매번 해석하고 수행해야 되므로 *<em>전체적인 속도는 느림 *</em></li>
</ul>
<h3 id="2-jit-컴파일러just-in-time-compiler">2) JIT 컴파일러(Just-In-Time Compiler)</h3>
<ul>
<li>인터프리터의 단점(속도, 효율)을 보완하기 위해 도입한 방식</li>
<li><strong>반복되는 코드를 런타임 중에 기계어로 컴파일하고 재사용하는 방식</strong></li>
<li>한번 기계어로 컴파일하여 캐싱 한 코드를 실행하므로 속도가 비교적 빠름</li>
</ul>
<blockquote>
<p>두 방식의 장단점이 있기 때문에 어떤 방식을 사용할지는 <code>컴파일 임계치</code>라는것을 계산하여 결정한다. 메서드가 호출된 횟수와 메서드가 종료된 횟수를 통해 컴파일 임계치를 계산하고, 이를 초과하면 JIT 방식으로 기계어를 캐싱하게 된다.</p>
</blockquote>
<h3 id="3-가비지-컬렉터">3) 가비지 컬렉터</h3>
<p>실행엔진에는** 가비지 컬렉터<strong>도 포함되어 있다.
**가비지 컬렉터는 자동으로 메모리를 관리해주는 JVM 기능으로, Heap 메모리 영역에서 더 이상 사용하지 않는 메모리를 자동으로 회수해 준다.</strong>
C언어 같은 경우는 직접 개발자가 메모리를 해제해줘야 하지만, JAVA는 이 가비지 컬렉터를 이용하여 자동으로 메모리를 실시간 최적화 시켜준다.</p>
<hr />
<h2 id="4️⃣네이티브-인터페이스jni">4️⃣네이티브 인터페이스(JNI)</h2>
<p>JNI는 <strong>자바가 다른 언어로 만들어진 어플리케이션과 상호 작용할 수 있는 인터페이스를 제공하는 프로그램</strong>이다.
C, C++ 등으로 작성된 라이브러리를 실행할 때 주로 사용하며, JAVA를 통해 실행할 수 없는 하드웨어, OS의 기능에 접근할 수도 있다.</p>
<h2 id="네이티브-메서드-라이브러리native-method-libraries">네이티브 메서드 라이브러리(Native Method Libraries)</h2>
<p>네이티브 메서드 라이브러리는 C, C++로 작성된 라이브러리를 뜻한다.
만일 헤더가 필요하다면 JNI는 이 라이브러리를 로딩해 실행한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/776c0e33-f971-404d-9212-a2fb4ddcd6a4/image.png" /></p>
<hr />
<h1 id="☑️다시보는-jvm-실행과정">☑️다시보는 JVM 실행과정</h1>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/843ecd67-390f-41e2-b626-9dfb2f824d6f/image.png" /></p>
<h4 id="1-자바-프로그램을-실행하면-jvm은-os로부터-메모리를-할당받는다">1. 자바 프로그램을 실행하면 JVM은 OS로부터 메모리를 할당받는다.</h4>
<h4 id="2-자바-컴파일러javac가-자바-소스코드java를-자바-바이트-코드class로-컴파일-한다">2. 자바 컴파일러(javac)가 자바 소스코드(.java)를 자바 바이트 코드(.class)로 컴파일 한다.</h4>
<h4 id="3-클래스-로더는-동적-로딩을-통해-필요한-클래스들을-로딩-및-링크하여-런타임-데이터-영역에-올린다">3. 클래스 로더는 동적 로딩을 통해 필요한 클래스들을 로딩 및 링크하여 런타임 데이터 영역에 올린다.</h4>
<h4 id="4-런타임-데이터-영역에-로딩된-바이트-코드는-실행-엔진을-통해-해석된다">4. 런타임 데이터 영역에 로딩된 바이트 코드는 실행 엔진을 통해 해석된다.</h4>
<h4 id="5-이-과정에서-실행-엔진에-의해-가비지-컬렉터의-작동과-스레드-동기화가-이루어진다">5. 이 과정에서 실행 엔진에 의해 가비지 컬렉터의 작동과 스레드 동기화가 이루어진다.</h4>