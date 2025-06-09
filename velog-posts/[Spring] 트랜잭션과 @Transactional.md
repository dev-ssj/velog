<h2 id="💡트랜잭션이란">💡트랜잭션이란</h2>
<blockquote>
<p>트랜잭션이란 데이터베이스에서 하나의 작업 단위를 뜻한다. 
트랜잭션에서는 여러 작업을 하나의 단위로 묶어서 전부 성공하거나, 전부 실패하게 처리한다.</p>
</blockquote>
<hr />
<h3 id="✅트랜잭션의-도입-배경">✅트랜잭션의 도입 배경</h3>
<p><strong>1. 데이터 처리의 신뢰성 부족</strong>
과거의 프로그램이나 단순한 파일 처리 기반 시스템에서는 여러 작업을 순차적으로 실행하다가 중간에 하나라도 실패하면 전체 데이터가 불일치하게 되는 문제가 많았다.
예를 들어 은행 시스템에서는 아래와 같은 일이 발생할 수 있다.</p>
<blockquote>
<ul>
<li>A가 B에게 10만원 이체- 시스템은 A 계좌에서 10만원을 출금</li>
</ul>
</blockquote>
<ul>
<li><p>그런데 시스템 오류로 B 계좌에 입금하지 못함
→ A는 돈을 잃고, B는 돈을 받지 못함(데이터 불일치)</p>
<p>이처럼 작업의 일부만 처리되면 시스템 신뢰성에 치명적인 문제가 발생하게 된다.</p>
</li>
<li><p><em>2. 일관성과 무결성의 보장 필요 *</em>
DB에는 여러 제약 조건(외래키, 유일성 등)이 존재하지만 여러개의 관련 작업을 하나로 묶어 처리할 수 없다면 이런 제약조건도 무용지물이 된다.
또한, 동시에 여러 사용자가 데이터를 수정하는 경우에는 예상치 못한 충돌이나 오류가 쉽게 발생한다.</p>
</li>
</ul>
<blockquote>
<p>➡️ <strong>따라서, 시스템은 다음과 같은 특성을 보장할 수 있어야 했다.</strong></p>
</blockquote>
<ul>
<li><p>중간에 실패하면 이전 상태로 되돌릴 수 있어야함(Rollback)</p>
</li>
<li><p>여러 작업을 하나의 흐름으로 묶을 수 있어야 함</p>
</li>
<li><p>작업 도중 다른 사용자에게 노출되지 않아야함(Isolation)</p>
<blockquote>
<p>이러한 문제를 해결하기 위해 <strong>트랜잭션(Transaction)</strong>이라는 개념이 도입되었다.
트랜잭션은 <strong>여러작업을 하나의 논리적인 작업 단위</strong>로 묶어 처리하며, 다음과 같은 특징(ACID)를 가진다.</p>
</blockquote>
<table>
<thead>
<tr>
<th>특성</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td>원자성(Atomicity)</td>
<td>전부 성공하거나 전부 실패해야 함</td>
</tr>
<tr>
<td>일관성(Consistency)</td>
<td>트랜잭션 전후 DB 상태가 일관되어야 함</td>
</tr>
<tr>
<td>고립성(Isolation)</td>
<td>동시에 실행되는 트랜잭션이 서로 간섭하지 않음</td>
</tr>
<tr>
<td>지속성(Durability)</td>
<td>성공한 트랜잭션은 영구히 반영됨</td>
</tr>
</tbody></table>
</li>
</ul>
<hr />
<h2 id="💡transactional">💡@Transactional</h2>
<blockquote>
<p>Spring에서는 트랜잭션을 관리하기 위해 <code>@Transactional</code> 애너테이션을 사용한다.
*<em>이 애너테이션을 메서드나 클래스에 붙이면, 트랜잭션 기능이 적용된 프록시 객체가 생성되며, 트랜잭션 성공 여부에 따라 Commit 또는 Rollback 작업이 이루어진다. *</em></p>
</blockquote>
<pre><code class="language-java">@Service
public class OrderService {

    @Transactional
    public void placeOrder(OrderRequest request) {
        product.decreaseStock(request.getQuantity());  // 재고 감소
        orderRepository.save(request.toEntity());      // 주문 저장
    }
}</code></pre>
<ul>
<li>위 메서드에서 예외가 발생하면 자동으로 <strong>롤백(rollback)</strong>된다.</li>
<li>아무 예외 없이 정상적으로 끝나면 <strong>커밋(commit)</strong>된다.</li>
</ul>
<hr />
<h3 id="✅transactional-도입-배경">✅@Transactional 도입 배경</h3>
<p>전통적인 트랜잭션은 아래처럼 코드레벨에 명시적으로 작성했어야 했다.</p>
<pre><code class="language-java">try {
    connection.setAutoCommit(false);

    // 여러 DB 작업 수행
    ...

    connection.commit();
} catch (Exception e) {
    connection.rollback();
}</code></pre>
<p>이 방식은 코드가 장황하고 반복적이며, 트랜잭션 처리 로직과 비즈니스 로직이 섞여 관심사가 분리되지 않는다.
➡️그래서 <strong>선언적 트랜잭션 처리방식인 @Transactional이 등장했다.</strong></p>
<hr />
<h3 id="✅transactional의-작동-원리--흐름">✅@Transactional의 작동 원리 &amp; 흐름</h3>
<p>스프링은 @Transactional 어노테이션이 붙은 메서드가 호출되면, 자동으로 트랜잭션을 시작하고, 종료 시에 커밋하거나 롤백한다.
이러한 과정은 프록시 패턴을 사용해 구현되며, AOP(관점 지향 프래그래밍) 기반으로 동작한다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/105aa793-cccb-416a-974c-7aeca91cda3b/image.png" /></p>
<p><strong>1. 프록시 객체의 생성</strong>
@Transactional이 선언된 클래스나 메서드가 있을 경우, 스프링은 이에 대한 <strong>프록시 객체를 생성</strong>한다. 이 프록시 객체는 대상 객체를 감싸면서, 해당 메서드 호출 전 후에 필요한 트랜잭션 처리를 수행하는 역할을 한다.</p>
<p><strong>2. 트랜잭션의 시작</strong>
프록시 객체를 통해 @Trasactional이 붙은 메서드가 호출되면, 트랜잭션 관리자는 새로운 트랜잭션을 시작한다.</p>
<p><strong>3. 영속성 컨텍스트의 생성 및 연결</strong>
트랜잭션이 시작되면, JPA를 사용할 경우 <strong>영속성 컨텍스트</strong>가 생성되고, 트랜잭션을 연결한다. 이렇게 만들어진 영속성 컨텍스트는 트랜잭션 범위의 영속성 컨텍스트라고 한다.
즉, 트랜잭션이 시작할 때 생성되고 트랜잭션이 종료될 때까지 유지된다.</p>
<p><strong>4. 비즈니스 로직 실행</strong>
위와 같은 단계를 통해 트랜잭션이 시작되고 영속성 컨텍스트가 준비되면 실제 비즈니스 로직이 실행된다.</p>
<p><strong>5. 커밋 또는 롤뱃</strong>
비즈니스 로직의 실행이 정상적으로 끝나면, 트랜잭션 관리자는 트랜잭션을 커밋하고 영속성 컨텍스트를 종료한다.
이 과정에서 Dirth Checking이 일어나 변경된 도메인 객체에 대한 SQL이 DB에 반영된다.
만약 비즈니스 로직 실행 도중 예외가 발생하면 트랜잭션은 롤백되고 해당 트랜잭션 범위의 영속성 컨텍스트 또한 종료된다.</p>
<hr />
<h3 id="✅transactional-사용-위치">✅@Transactional 사용 위치</h3>
<ul>
<li><p>클래스 또는 메서드에 선언 가능</p>
</li>
<li><p>클래스에 선언하면 <strong>모든 public 메서드</strong>에 적용</p>
<pre><code class="language-java">@Service
@Transactional // 클래스 전체에 적용
public class UserService {

  public void createUser(UserDto dto) {

  }

  @Transactional(readOnly = true) // 메서드별 재정의도 가능
  public User getUser(Long id) {
      return userRepository.findById(id).orElseThrow(...);
  }
}</code></pre>
</li>
</ul>
<hr />
<h3 id="✅transactional-주요-사용-옵션">✅@Transactional 주요 사용 옵션</h3>
<p>** 1. readOnly = true**</p>
<blockquote>
<ul>
<li>트랜잭션을 읽기 전용 모드로 변경하는 옵션</li>
</ul>
</blockquote>
<ul>
<li>JPA의 <strong>Drity Checking(변경 감지) 기능 비활성화</strong> → 성능 향상</li>
<li><strong>SELECT 전용 로직</strong>에서 사용 권장(데이터를 수정하는 서비스에 적용하면 안됨)</li>
</ul>
<p>*<em>📍사용 예시 *</em></p>
<pre><code class="language-java">@Transactional(readOnly = true)
public User getUser(Long id) { ... }</code></pre>
<hr />
<p>** 2. rollbackFor, noRollbackFor **</p>
<blockquote>
<ul>
<li>특정 트랜잭션에서 rollback이 일어나지 않도록 하는 옵션</li>
</ul>
</blockquote>
<table>
<thead>
<tr>
<th>속성</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><code>rollbackFor</code></td>
<td>기본적으로 <strong>RuntimeException만 롤백</strong> → <code>CheckedException</code>도 롤백하려면 명시 필요</td>
</tr>
<tr>
<td><code>noRollbackFor</code></td>
<td>특정 예외는 롤백 제외 가능</td>
</tr>
</tbody></table>
<p>** 📍사용 예시 **</p>
<pre><code class="language-java">@Transactional(rollbackFor = SQLException.class)
public void updateData() throws SQLException { ... }</code></pre>
<hr />
<p>** 3. propagation(전파 속성)**</p>
<blockquote>
<ul>
<li>이미 트랜잭션이 진행중일 때 추가 트랜잭션 진행을 어떻게 할지 결정하는 것</li>
</ul>
</blockquote>
<table>
<thead>
<tr>
<th>값</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><strong>REQUIRED (기본값)</strong></td>
<td>이미 트랜잭션 있으면 참여, 없으면 새로 시작</td>
</tr>
<tr>
<td><strong>REQUIRES_NEW</strong></td>
<td>항상 새 트랜잭션 시작, 기존 트랜잭션은 일시 중단</td>
</tr>
<tr>
<td><strong>NESTED</strong></td>
<td>트랜잭션 내 중첩 트랜잭션 시작 (SAVEPOINT 생성)</td>
</tr>
<tr>
<td>SUPPORTS</td>
<td>트랜잭션 있으면 참여, 없으면 트랜잭션 없이 실행</td>
</tr>
<tr>
<td>NOT_SUPPORTED</td>
<td>트랜잭션 없이 실행 (기존 트랜잭션 일시 중단)</td>
</tr>
<tr>
<td>NEVER</td>
<td>트랜잭션 있으면 예외 발생</td>
</tr>
<tr>
<td>MANDATORY</td>
<td>반드시 기존 트랜잭션 존재해야 함, 없으면 예외 발생</td>
</tr>
</tbody></table>
<p>** 📍사용 예시 **</p>
<pre><code class="language-java">@Transactional(propagation = Propagation.REQUIRES_NEW)
public void logError() {
    // 예외가 발생해도 외부 트랜잭션에 영향을 주지 않음
}</code></pre>
<hr />
<p>** 4. isolcation(격리 수준) **</p>
<blockquote>
<ul>
<li>동시에 여러 트랜잭션이 처리될 때, 트랜잭션끼리 얼<strong>마나 서로 고립되어 있는지</strong>를 나타내는 것</li>
</ul>
</blockquote>
<ul>
<li>즉, 특정 트랜잭션이 다른 트랜잭션에 변경한 데이터를 볼 수 있도록 허용할지 말지를 결정하는 것이다.</li>
</ul>
<table>
<thead>
<tr>
<th>수준</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><strong>DEFAULT</strong></td>
<td>DB 기본값 사용</td>
</tr>
<tr>
<td><strong>READ_UNCOMMITTED</strong></td>
<td>커밋되지 않은 데이터 읽기 허용 → dirty read 발생</td>
</tr>
<tr>
<td><strong>READ_COMMITTED</strong></td>
<td>커밋된 데이터만 읽음 (SQL Server 기본값)</td>
</tr>
<tr>
<td><strong>REPEATABLE_READ</strong></td>
<td>같은 쿼리 결과 반복 보장</td>
</tr>
<tr>
<td><strong>SERIALIZABLE</strong></td>
<td>가장 엄격, 동시성 거의 없음 → 성능 저하</td>
</tr>
<tr>
<td>** 📍사용 예시 **</td>
<td></td>
</tr>
<tr>
<td>```java</td>
<td></td>
</tr>
<tr>
<td>@Transactional(isolation = Isolation.SERIALIZABLE)</td>
<td></td>
</tr>
<tr>
<td>public void updateInventory() { ... }</td>
<td></td>
</tr>
<tr>
<td>```</td>
<td></td>
</tr>
</tbody></table>