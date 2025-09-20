<h2 id="💡pojo란">💡POJO란?</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/b21f2ea0-dece-4f3f-a692-52c04817bbb2/image.png" /></p>
<blockquote>
<p><strong>Plain Old Java Object</strong>의 약자로, 특별한 규칙이나 프레임워크에 종속되지 않은 <strong>순수한 자바 객체</strong>를 의미한다.</p>
</blockquote>
<h2 id="✅pojo가-탄생하게-된-이유">✅POJO가 탄생하게 된 이유</h2>
<blockquote>
<p>POJO에 대한 개념은 복잡한 <strong>EJB에 대한 반발로 시작</strong>되었고, Java 개발의 방향을 바꿨을 정도로 큰 영향을 미쳤다.</p>
</blockquote>
<h3 id="1-ejb의-복잡함초기-java-ee">1. EJB의 복잡함(초기 Java EE)</h3>
<p> 당시에는 비즈니스 로직을 작성하려면 Enterprise JavaBeans (EJB)을 써야했다.
 하지만 EJB를 구현하려면 <strong>인터페이스, XML 설정, JNDI 등록, 컨테이너 종속 등 복잡한 절차</strong>가 필요했다.
 또한, 테스트도 어렵고, <strong>개발 생산성</strong>이 매우 떨어졌다.</p>
<h3 id="2-pojo-철학의-등장">2. POJO 철학의 등장</h3>
<p>2002년, Martin Fowler와 Rebecca Parsons가 <code>POJO</code>라는 개념을 언급하면서 개발자들 사이에서 주목을 받게 됐다.
<strong>프레임워크에 의존하지 않는 순수한 객체로도 충분하다는 주장</strong>이였다.</p>
<h2 id="✅pojo의-핵심철학">✅POJO의 핵심철학</h2>
<blockquote>
<p>객체는 프레임워크가 아니라 개발자가 주도적으로 설계해야 한다.</p>
</blockquote>
<ul>
<li>복잡한 기술 없이도 <strong>객체지향 설계</strong>를 적용할 수 있어야 하며</li>
<li><strong>단순한 구조</strong>를 통해 테스트와 유지보수를 쉽게해야 한다.</li>
</ul>
<h4 id="📍가장-기본적인-형태의-java객체-pojo">📍가장 기본적인 형태의 Java객체, POJO</h4>
<pre><code>public class MyPojo {
    private String name;
    private int age;

    public String getName() {
        return name;
    }
    public String getAge() {
        return age;
    }
    public void setName(String name) {
        this.name = name;
    }
    public void setAge(int age) {
        this.age = age;
    }
}</code></pre><h2 id="✅pojo의-조건">✅POJO의 조건</h2>
<ol>
<li><p>특정 규약에 종속되지 않는다
→꼭 필요한 API 외에는 종속되지 않아야 함
→단일 속성 원칙으로 객체 지향적인 설계 기법을 적용하기 어려워지는 문제 발생하게 됨</p>
</li>
<li><p>특정 환경에 종속되지 않는다</p>
</li>
<li><p>객체 지향 설계가 적용 되어야 한다</p>
</li>
</ol>
<h2 id="✅pojo의-장점">✅POJO의 장점</h2>
<ol>
<li>깔끔한 코드</li>
<li>자동화된 테스트에 매우 유리</li>
<li>객체지향적인 설계를 자유롭게 적용할 수 있다.</li>
</ol>
