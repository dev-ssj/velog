<h1 id="✅equals">✅equals()</h1>
<p><strong>equals 메서드는 특정 객체와 다른 객체가 같은 객체인지 판별해주는 메서드</strong>이다.</p>
<pre><code class="language-java">//String 리터럴은 String Intern Pool(문자열 상수 풀)에 저장
String s1 = &quot;Hello&quot;;
String s2 = &quot;Hello&quot;;

System.out.println(s1 == s2); // s1과 s2는 같은 리터럴 &quot;Hello&quot;를 가리키므로 같은 객체 참조를 공유 -&gt; true
System.out.println(s1.equals(s2)); //equlas()는 문자열의 내용(문자 시퀀스)를 비교하므로 true

//하지만 새로운 객체를 만든다면?
Person person1 = new Person(&quot;홍길동&quot;);
Person person2 = new Person(&quot;홍길동&quot;);

System.out.println(person1 == person2); // == 은 객체타입인경우 주소값을 비교 -&gt; 서로 다른 객체는 각각 다른 주소를 가지고 있으므로 false

System.out.println(person1.equals(person2)) // equals또한 객체타입인경우 주소값을 비교하기 때문에 false가 출력된다.</code></pre>
<p>위 코드를 보면 알다시피,** ==과 equlas()는 문자열이 아닌 클래스 자료형의 객체 데이터일경우 객체의 주소를 비교**한다.
즉, 컴퓨터 입장에서는 같은 <code>홍길동</code> 값을 가지고 있어도, 객체의 주소값이 다르므로 <code>person1</code>과 <code>person2</code>는 다른 객체라고 판단한 것이다.
하지만 우리 입장에서 <code>person1</code>의 저장된 홍길동이나, <code>person2</code>에 저장된 홍길동이나 같은 값이라고 판단할 수도 있을것이다.
이렇게 객체를 비교하는 방식은 크게 두가지로 나뉘게된다. </p>
<ul>
<li><strong>동일성</strong>(객체의 주소값을 직접 비교하는 방식(==이나 Object.equals())</li>
<li><strong>동등성</strong>(주소가 아닌 다른 어떤 값을 이용해 비교하는 방식(equlas()를 오버라이딩해서 사용)</li>
</ul>
<h3 id="1️⃣동일성-identity">1️⃣동일성 (Identity)</h3>
<p>비교 대상이 똑같다면 우리는 대상이 동일하다고 표현한다. 즉, <strong>객체의 주소값을 직접 비교하는 경우가 바로 객체의 동일성을 판단한다</strong>라고 말할 수 있다.
자바의 객체는 생성되는 동시에 메모리에 할당되므로, 서로 다른 변수는 서로 다른 메모리 주소값을 가진다. 따라서 객체와 주소는 대부분 1:1로 매칭되기에 주소가 같다면 동일하다고 판단할 수 있다.</p>
<h3 id="2️⃣동등성-equality">2️⃣동등성 (Equality)</h3>
<p>예를 들어, 수능 국어가 1등급 컷이 94점이라면 100점을 맞든, 94점을 맞든 성적표에는 1등급으로 찍힌다. 1등급 컷이 94점이므로 100점~94점이라면 1등급으로 분류되는 것이다. 이렇게 점수는 다르지만 의미상으로는 같은 1등급으로 분류되는 상황같이 <strong>실제로는 서로 다른 값이라도, 의미상으로 같다고 판별되는 경우를 동등하다</strong>라고 한다.</p>
<blockquote>
<p>정리하자면,** 프로그램을 구현하면서 다른 값을 맥락상 같다고 판단해야 하는 경우가 존재하며, 이를 객체의 동등성을 비교한다고 표현<strong>한다. **객체 동등성을 판단하는 기준은 객체마다 다르기 때문에 equals() 메서드를 오버라이딩하여 사용</strong>한다.</p>
</blockquote>
<p>하지만 equals()를 오버라이딩하여 사용할 때, equals()만 오버라이딩 한다면 아래와 같은 경고가 뜰 것이다. 이 경고문의 내용은 <code>equals()</code> 는 오버라이딩 하면서 왜 <code>hashCode()</code> 는 오버라이딩 안하냐는 경고이다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/f49b9284-d2ad-4926-84d7-380b34ebfbd7/image.png" /></p>
<p>경고가 뜨는 이유는, 객체의 주소가 아닌 객체의 필드 값을 비교하기 위해 <code>equals()</code> 를 오버라이딩 시킨다면 당연히 <code>hashCode</code>도 같이 객체의 필드를 다루도록 오버라이딩 해야되기 때문이다. 왜냐하면 <strong>equals() 의 결과가 true인 두 객체의 해시코드는 반드시 같아야한다는 자바의 규칙때문이다.</strong></p>
<hr />
<h1 id="🤔그렇다면-hashcode는-뭐길래">🤔그렇다면 hashCode()는 뭐길래?</h1>
<p>위에서 <code>equals()</code>의 결과가 true라면 두 객체의 해시코드는 반드시 같아야한다고 설명했다.
여기서 객체의 해시코드란 객체를 식별하는 하나의 정수값을 말한다. Object의 <code>hashCode()</code> 메소드는 <strong>객체의 메모리 번지를 이용해서 해시코드를 만들어 리턴</strong>하기 때문에 객체 마다 다른 값을 가지고 있다. 
객체의 값을 동등성 비교시, hashCode()를 오버라이딩해야하는데 컬렉션 프레임워크에서 HashSet, HashMap, HashTable은 다음과 같은 방법으로 두 객체가 동등한지 비교한다.</p>
<p>먼저 <code>hashCode()</code>메소드를 실행해서 리턴된 해시코드 값이 같은지를 본다. 해시 코드값이 다르면 다른 객체로 판단하고, 해시 코드값이 같으면 <code>equals()</code>메소드로 다시 비교한다. 이 두 개가 모두 맞아야 동등 객체로 판단한다. 즉, <strong>해시코드 값이 다른 엔트리끼리는 동등성 비교를 시도조차 하지 않는다.</strong>
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/e2c82219-b99e-4397-9d03-585f5397d58a/image.png" /></p>
<hr />
<h1 id="✅equals와-hashcode를-같이-재정의해야-하는-이유">✅equals()와 hashCode()를 같이 재정의해야 하는 이유</h1>
<p>hashCode()는 독립적으로 사용되기보다 HashMap와 같은 hash table을 지원하기 위해 만들어진 메서드이다. 해시 맵에 값을 저장할 때는 Key-value의 쌍을 만들어 저장하며, 이후 value를 검색할 때는 key를 통해 접근한다.</p>
<p><strong>그리고 Java에서 해시 테이블 자료구조의 key는 모두 hashCode()를 통해 만들어진다.</strong>
*<em>즉, 모든 객체의 해시코드 값은 다르다. *</em></p>
<p><strong>equals()를 재정의한다는 것은 곧 객체의 같고 다름을 비교하는 어떤 논리적 기준이 생긴다</strong>는 뜻이며, <strong>이 기준에 맞추어 객체의 해시코드를 생성해야 해시테이블 자료구조를 사용할 때 의도에 맞게 key에 접근할 수 있기에 hashCode()도 그에 맞춰 재정의</strong>해야 하는 것이다.</p>
<hr />
<h4 id="☑️equals와-hashcode의-관계에-대해-설명하세요">☑️equals()와 hashCode()의 관계에 대해 설명하세요</h4>
<p>우선<code>equals()</code>는 어떤 두 객체가 같은 객체인지 판별해주는 메서드입니다.
<code>equals()</code>로 동등성 검사를 할 때, <code>equals()</code>와<code>hashCode()</code>는 항상 함께 재정의를 해줘야 합니다. 그 이유는 해시 기반 컬렉션인 HashMap, HashSet이 내부적으로 원소 비교를 할때, 먼저 hashCode()로 같은 버킷인지 판단하고, 이후에 equals()로 동등성을 최종확인하기 때문입니다.
즉, 해시 기반 컬렉션의 동작을 위해 equals와 hashCode는 같은 필드를 기준으로 일관되게 구현해야 하며, 규약을 지키지 않으면 데이터 중복, 검색 실패 같은 문제가 발생합니다.</p>