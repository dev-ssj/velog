<h2 id="💡객체지향-설계-원칙이란">💡객체지향 설계 원칙이란?</h2>
<blockquote>
<p>객체지향 설계 원칙이란 <strong>소프트웨어 개발의 유지보수성과 확장성을 높이기 위해</strong> 도입된 일련의 설계 지침이다.
이 원칙은 시간이 지나도 변화에 유연하게 대응할 수 있는 구조 즉, <strong>변경에 강하고 확장에 유연한 코드</strong>를 만들기 위해 등장했다.</p>
</blockquote>
<hr />
<h2 id="✅객체지향-설계-원칙의-도입-배경">✅객체지향 설계 원칙의 도입 배경</h2>
<p>초기 소프트웨어 개발에서는 아래와 같은 문제들이 자주 발생했다.</p>
<p><strong>1. 코드의 강한 결합도(Tightly Coupled Code)</strong>
→ 하나의 클래스가 변경되면 여러 클래스가 연쇄적으로 영향을 받는 구조로, 작은 변경이 전체 시스템에 영향을 주는 경우가 많았음.</p>
<p><strong>2. 유지보수의 어려움</strong>
→ 시간이 지날수록 코드가 복잡해지고, 누가 작성했는지 모르는 코드가 많아져 수정이 어렵고 버그도 많아짐.</p>
<p><strong>3. 재사용성 부족</strong>
→ 특정 기능을 다른 프로젝트나 모듈에서 재활용하기 어려움.</p>
<p><strong>4. 확장에 취약한 구조</strong>
→ 기능이 늘어날수록 기존 코드를 수정해야만 했고, 새로운 요구사항이 들어올 때마다 기존 코드가 깨지기 쉬웠음.</p>
<blockquote>
<p>➡️이러한 문제들을 해결하기 위해 <strong>객체지향 프로그래밍(OOP)</strong>이 제안되었고, OOP 안에서도 <strong>더 좋은 설계를 위한 가이드라인</strong>으로써 SOLID 원칙을 중심으로 한 <strong>객체지향 설계 원칙</strong>들이 제안되었다.</p>
</blockquote>
<hr />
<h2 id="✅객체지향-설계-원칙-solid">✅객체지향 설계 원칙 (SOLID)</h2>
<blockquote>
<p><strong>SOLID 원칙</strong>은 로버트 C. 마틴이 제안한 객체지향 설계 5대 원칙이다. 
SOLID의 용어 개념 이론들은 모두 자바의 클래스 객체 지향인 <strong>추상화, 상속, 인터페이스, 다형성</strong> 등의 개념들을 재정립한 것으로 보면 된다.
그리고 이 5가지 원칙들은 서로 독립된 개별적인 개념이 아니라 서로 <strong>개념적으로 연관</strong>되어 있다.</p>
</blockquote>
<h3 id="1-단일-책임-원칙---srpsingle-responsibility-principle">1. 단일 책임 원칙 - SRP(Single Responsibility Principle)</h3>
<blockquote>
<p> ** 한 클래스는 하나의 책임만 가져야 한다.**</p>
</blockquote>
<p>➡️클래스는 오직** 한가지 기능 또는 역할<strong>만을 수행해야하며, **변경 사유도 하나뿐</strong>이여야 한다.</p>
<h4 id="❌나쁜-예">❌나쁜 예</h4>
<pre><code class="language-java">public class UserManager {
    public void createUser() { ... }
    public void deleteUser() { ... }
    public void saveToFile() { ... }  
}</code></pre>
<p>→ 위 클래스에서는 사용자 관리(createUser, deleteUser)와 파일관리(saveToFile)이라는 두가지 책임을 가진다.</p>
<h4 id="⭕좋은-예">⭕좋은 예</h4>
<pre><code class="language-java">public class UserService {
    public void createUser() { ... }
}

public class FileSaver {
    public void saveToFile() { ... }
}</code></pre>
<p>→ 클래스가 각각 하나의 책임만 갖도록 분리</p>
<hr />
<h3 id="2-개방-폐쇄-원칙---ocpopenclosed-principle">2. 개방-폐쇄 원칙 - OCP(Open/Closed Principle)</h3>
<blockquote>
<p>** 확장에는 열려있고, 변경에는 닫혀있어야 한다.**</p>
</blockquote>
<p>➡️기존 코드를 건드리지 않고 새로운 기능을 추가할 수 있어야 한다. 즉, <strong>변경이 아닌 확장</strong>으로 해결하라는 의미이다.</p>
<h4 id="❌나쁜-예-1">❌나쁜 예</h4>
<pre><code class="language-java">public class DiscountService {
    public double getDiscount(String userType) {
        if (userType.equals(&quot;VIP&quot;)) return 2;
        else if (userType.equals(&quot;BASIC&quot;)) return 1;
        return 0;
    }
}</code></pre>
<p>→ 새로운 회원 등급이 생길 때마다 if문을 수정해야 한다.</p>
<h4 id="⭕좋은-예-1">⭕좋은 예</h4>
<pre><code class="language-java">public interface DiscountPolicy {
    double getDiscount();
}

public class VipDiscount implements DiscountPolicy {
    public double getDiscount() { return 2; }
}

public class BasicDiscount implements DiscountPolicy {
    public double getDiscount() { return 1; }
}</code></pre>
<p>→ 새로운 등급이 추가되어도 기존 코드는 변경하지 않고, 코드 추가만 하면 된다.</p>
<hr />
<h3 id="3-리스코프-치환-원칙---lspliskov-substitution-principle">3. 리스코프 치환 원칙 - LSP(Liskov Substitution Principle)</h3>
<blockquote>
<p><strong>자식 클래스는 부모 클래스를 대체할 수 있어야 한다.</strong></p>
</blockquote>
<p>➡️자식 클래스를 사용하는 클라이언트 코드에서 부모 클래스와 동일하게 작동해야 한다. 즉, 부모 클래스와 자식 클래스 사이의 행위에는 <strong>일관성</strong>이 있어야한다.</p>
<h4 id="❌나쁜-예-2">❌나쁜 예</h4>
<pre><code class="language-java">public class Bird {
    public void fly() { ... }
}

public class Ostrich extends Bird {
    public void fly() { throw new UnsupportedOperationException(); }
}</code></pre>
<p>→ 타조는 날 수 없는데 Bird를 상속받음</p>
<h4 id="⭕좋은-예-2">⭕좋은 예</h4>
<pre><code class="language-java">public interface Bird { }

public interface Flyable {
    void fly();
}</code></pre>
<p>→ 인터페이스 분리로 리스코프 위반 방지<strong>(다형성)</strong></p>
<hr />
<h3 id="4-인터페이스-분리-원칙---ispinterface-segregation-principle">4. 인터페이스 분리 원칙 - ISP(Interface Segregation Principle)</h3>
<blockquote>
<p><strong>클라이언트는 자신이 사용하지 않는 인터페이스에 의존하면 안 된다.</strong></p>
</blockquote>
<p>➡️인터페이스는 작고 명확하게 분리되어야 한다. 즉, 너무 많은 기능을 한 인터페이스에 몰아 넣지 말고, <strong>필요한 기능만 분리해서 사용</strong>하라는 뜻이다.</p>
<h4 id="❌나쁜-예-3">❌나쁜 예</h4>
<pre><code class="language-java">public interface Worker {
    void work();
    void eat();
    void sleep();
}</code></pre>
<p>→ 여러 기능을 하나의 인터페이스에서 사용</p>
<h4 id="⭕좋은-예-3">⭕좋은 예</h4>
<pre><code class="language-java">public interface Workable {
    void work();
}

public interface Eatable {
    void eat();
}

public interface sleepable {
    void sleep();
}</code></pre>
<hr />
<h3 id="5-의존-역전-원칙---dipdependency-inversion-principle">5. 의존 역전 원칙 - DIP(Dependency Inversion Principle)</h3>
<blockquote>
<p><strong>클라이언트는 구체 클래스가 아닌 인터페이스나 추상 클래스에 의존해야 한다.</strong></p>
</blockquote>
<p>➡️구체 클래스가 아닌 <strong>추상화에 의존함으로써 유연한 시스템 구조</strong>를 만들 수 있다.
의존 역전 원칙은 객체 생성 방식에 있어** new를 직접 쓰지말고 DI(의존성 주입)를 활용**하라는 원칙과도 연결 된다.</p>
<h4 id="❌나쁜-예-4">❌나쁜 예</h4>
<pre><code class="language-java">public class OrderService {
    private MySQLDatabase db = new MySQLDatabase();  // 강한 의존
}</code></pre>
<h4 id="⭕좋은-예-4">⭕좋은 예</h4>
<pre><code class="language-java">public class OrderService {
    private final Database db;

    public OrderService(Database db) {
        this.db = db;
    }
}</code></pre>
<p>→ 인터페이스에 의존하고, 구체 구현은 외부에서 주입받음 (ex: <strong>스프링의 DI</strong>)</p>
<hr />
<h2 id="✅객체지향-설계-원칙이-필요한-이유">✅객체지향 설계 원칙이 필요한 이유</h2>
<h4 id="1-변경이-잦은-요구사항에-대응할-수-있다">1. 변경이 잦은 요구사항에 대응할 수 있다.</h4>
<ul>
<li>기능이 바뀌어도 기존 코드를 수정하지 않고 새로운 코드만 추가하도록 설계 가능하다.</li>
</ul>
<h4 id="2-협업-및-유지보수-용이">2. 협업 및 유지보수 용이</h4>
<ul>
<li>각 클래스/모듈의 책임이 명확해져 다른 개발자가 코드를 이해하고 수정하기 쉽다.</li>
</ul>
<h4 id="3-테스트-가능성-향상">3. 테스트 가능성 향상</h4>
<ul>
<li>의존성이 줄고 구조가 단순해져 단위 테스트 및 Mock 테스트가 용이해진다.</li>
</ul>
<h4 id="4-재사용성-증가">4. 재사용성 증가</h4>
<ul>
<li>잘 분리된 기능은 다른 프로젝트나 모듈에서도 쉽게 재사용이 가능하다.</li>
</ul>
<h4 id="5-버그-감소">5. 버그 감소</h4>
<ul>
<li>변경이 한졍된 영역에만 영향을 주기 때문에 오류 가능성이 줄어든다.</li>
</ul>