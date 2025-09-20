<h1 id="🤔jdk-jre-jvm이-뭔데">🤔JDK, JRE, JVM이 뭔데?</h1>
<blockquote>
<p><strong>JDK와 JRE, JVM은 자바 개발과 실행에 관련된 핵심 요소</strong>로, 서로 밀접한 관계가 있는 요소들이다. 오늘은 JDK와 JRE, JVM이 뭔지, 그 차이점은 뭔지에 대해알아보자.</p>
</blockquote>
<h1 id="✅jdkjava-development-kit">✅JDK(Java Development Kit)</h1>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/0951dddd-046b-4ff9-82e4-d98817057443/image.png" /></p>
<p>JDK는 <strong>자바 개발키트(Java Development Kit)</strong>의 약자로, <strong>개발자들이 자바로 개발하는데 사용되는 SDK 키트</strong>라고 생각하면 된다.
JDK안에는 자바로 개발할 때 필요한 라이브러리들과 javac, javadoc 등의 개발 도구들을 포함되어 있고, 개발을 하려면 자바 프로그램을 실행도 시켜줘야 하기 때문에 뒤에서 배울 JRE(Java Runtime Environment)도 함께 포함되어 있다.
위 JDK 구조를 보면 알다시피, <strong>JDK 안에는 JRE와 JVM이 포함되어 있다.</strong>
➡️<strong>Java로 개발한 프로그램을 컴파일하여 만들어지는 바이트코드(*.class)를 실행시키기 위한 가상 머신</strong></p>
<h2 id="📁jdk-디렉터리-구조">📁JDK 디렉터리 구조</h2>
<ul>
<li>bin : 자바 개발과 실행에 필요한 도구와 유틸리티 명령</li>
<li>include : 네이티브 코드 프로그래밍에 필요한 C언어 헤더 파일</li>
<li>lib : 실행에 필요한 라이브러리 클래스</li>
</ul>
<h2 id="🧰bin-디렉터리에-들어있는-주요-개발-소프트웨어">🧰bin 디렉터리에 들어있는 주요 개발 소프트웨어</h2>
<ul>
<li>javac : <strong>자바 컴파일러</strong>. 자바 소스를 바이트 코드로 변환(컴파일)</li>
<li>java  : <strong>자바 인터프리터</strong>. 컴파일러가 생성한 바이트 코드를 해석하고 실행</li>
<li>javadoc : 자바 소스로부터 HTML 형식의 API 도큐먼트 생성</li>
<li>jar : 자바 클래스 파일을 압축한 아카이브 파일 생성, 관리하는 압축 프로그램</li>
<li>jmod : 자바의 모듈 파일(.jmd)을 만들거나 모듈 파일의 내용 출력</li>
<li>jlink : 응용 프로그램에 맞춘 맞춤형 JRE 생성</li>
<li>jdb : 자바 응용프로그램의 실행 중 오류를 찾는데 사용하는 디버거</li>
<li>javap : 역어셈블러. 컴파일된 클래스 파일을 원래의 소스로 변환</li>
</ul>
<hr />
<h1 id="✅jrejava-runtime-environment">✅JRE(Java Runtime Environment)</h1>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/66f6361a-0443-4451-80aa-1488761b51ec/image.png" />
JRE란 <strong>자바 실행환경(Java Runtime Environment)</strong>의 약자로서** 자바 프로그램을 실행하기 위해 필요한 JVM과 자바 클래스 라이브러리, 그리고 다른 지원 파일들을 포함<strong>한다. JRE만으로 Java 프로그램 실행은 가능하지만, JRE 자체에는 컴파일러가 없기 때문에 자바 프로그램 개발은 불가능하다. 즉, **JRE은 JVM을 동작하기 위한 자바 실행환경</strong>이라고 생각하면 된다.
➡️<strong>Java로 프로그램을 직접 개발하려면 JDK가 필요하고, 컴파일 된 Java 프로그램을 실행시키려면 JRE가 필요하다!</strong></p>
<hr />
<h1 id="✅jvmjava-virtual-machine">✅JVM(Java Virtual Machine)</h1>
<p>JVM은 <strong>자바 가상머신(Java Virtual Machine)</strong>의 약자로서, <strong>자바 바이트 코드를 각 운영체제에 맞는 기계어로 바꾸어 실행하는 가상머신</strong>이며, JRE에 포함되어 있다.
JVM이 운영체제 위에서 자바 바이트 코드를 받아 각 운영체제에 맞는 기계어로 바꿔주기 때문에 <strong>OS에 종속적이지 않고, 플랫폼 독립성을 제공한다.</strong></p>
<blockquote>
<p>📌<strong>자바 바이트 코드란?</strong>
우리가 자바 파일을 만들면 .java라는 확장자의 파일이 생기게 된다. 이 파일을 컴파일 하면 .class 확장자를 가지는 파일을 얻을 수 있는데, 이 파일이 자바 바이트 코드로 이루어진 파일이다. 이러한 java파일을 컴파일할 수 있는 컴파일러는 JDK에 포함되어 있다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/f2f81ff8-480a-4772-83c5-b773fc264c92/image.png" /></p>
<hr />
<h1 id="✅자바-프로그램의-실행-과정">✅자바 프로그램의 실행 과정</h1>
<blockquote>
<p>자바는 JVM이라는 특성때문에 자바로 작성한 파일을 실행하기 위해서는 두번의 과정을 거쳐야만한다. 이 과정을 알아보도록 하자.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/120a4d4c-7b67-475c-aee2-fd955d2e13d3/image.png" /></p>
</blockquote>
<h3 id="1️⃣자바-소스코드를-작성한다hellojava">1️⃣자바 소스코드를 작성한다(Hello.java)</h3>
<h3 id="2️⃣컴파일러complier가-자바-소스코드를-이용하여-클래스-파일을-생성한다helloclass">2️⃣컴파일러(Complier)가 자바 소스코드를 이용하여 클래스 파일을 생성한다(Hello.class)</h3>
<ul>
<li>컴파일된 클래스 파일은 JVM이 인식할 수 있는 바이트 코드 파일이다<h3 id="3️⃣jvm이-클래스-파일의-바이트-코드를-해석하여-바이너리-코드로-변환하고-프로그램을-수행한다">3️⃣JVM이 클래스 파일의 바이트 코드를 해석하여 바이너리 코드로 변환하고 프로그램을 수행한다.</h3>
</li>
<li>이 과정에서 JVM내부에서 실행되는 단계가 많다.</li>
<li>Class Loader가 .class를 메모리에 로딩하고</li>
<li>Execution Engine이 바이트코드를 해석 or JIT 컴파일하여 CPU가 실행한다.</li>
<li>그 후 프로그램 실행 중 객체는 Heap에 생성, 메서드는 Stack에서 실행된다.</li>
<li>더이상 참조되지 않는 객체는 GC에 의해 정리된다.</li>
<li><em>👉 JVM의 구조는 추후 따로 포스팅할 예정!*</em><h3 id="4️⃣hello-수행-결과가-컴퓨터에-반영된다">4️⃣Hello 수행 결과가 컴퓨터에 반영된다.</h3>
</li>
</ul>
<hr />
<h1 id="☑️jdk-jre-jvm의-핵심-차이">☑️JDK, JRE, JVM의 핵심 차이</h1>
<h2 id="1-jvmjava-virtual-machine">1. JVM(Java Virtual Machine)</h2>
<ul>
<li>자바 바이트 코드(.class)를 실행하는 가상 머신</li>
<li>os 위에서 동작하며 바이트 코드를 기계어로 변환하여 실행 -&gt; 플랫폼 독립성</li>
</ul>
<h2 id="2-jrejava-runtime-environment">2. JRE(Java Runtime Environment)</h2>
<ul>
<li>자바 프로그램을 실행하기 위한 환경</li>
<li>JVM + 자바 라이브러리</li>
<li>자바 프로그램 실행은 가능하지만, 개발(컴파일)은 불가능 -&gt; 개발하려면 JDK 필요</li>
</ul>
<h2 id="3-jdkjava-development-kit">3. JDK(Java Development Kit)</h2>
<ul>
<li>자바 개발 도구(컴파일러, 디버거 등) 모음</li>
<li>JRE + 컴파일러(javac) + 디버거 + javadoc 등 </li>
</ul>
<blockquote>
<p>👉 <strong>JVM은 자바 바이트 코드를 실행하는 가상 머신</strong>이며, <strong>JRE는 JVM과 기본 라이브러리를 포함한 실행환경</strong>이다. <strong>JDK는 JRE에 컴파일러, 디버거 등 개발 도구를 더한 것</strong>으로, 자바 프로그램을 실행하려면 JRE만 있어도 되지만 개발을 위해서는 JDK가 필요하다!</p>
</blockquote>