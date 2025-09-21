<h1 id="💡final">💡final</h1>
<ul>
<li><strong>변수, 메서드, 클래스에 사용될 수 있는 제어자</strong></li>
<li>변수에 사용 : 변수의 값이 변경될 수 없음을 의미(값을 한번만 할당 가능)</li>
<li>메서드에 사용 : 오버라이딩 할 수 없음을 나타냄</li>
<li>클래스에 사용 : 상속을 금지하는 역할<pre><code class="language-java">final int num = 10;
final class MyClass {...}
final void myMethod() {...}</code></pre>
</li>
</ul>
<hr />
<h1 id="💡finally">💡finally</h1>
<ul>
<li><strong>try-catch</strong> 블록과 함께 사용됨</li>
<li><strong>예외 발생 여부와 상관없이 항상 실행되는 코드</strong></li>
<li>주로 리소스 해제 및 정리 작업에 사용<pre><code class="language-java">try {
          int result = 10 / 0; // 예외 발생
          System.out.println(result);
      } catch (ArithmeticException e) {
          System.out.println(&quot;예외 발생!&quot;);
      } finally {
          System.out.println(&quot;finally block&quot;);
      }</code></pre>
</li>
<li>출력 결과 : 예외 발생!/n finally block</li>
</ul>
<hr />
<h1 id="💡finalize">💡finalize</h1>
<ul>
<li><p>객체가 가비지 컬렉터에 의해 소멸되기 직전에 호출되는 메서드</p>
</li>
<li><p>객체의 리소스를 해제하거나 정리하는 작업에서 사용</p>
</li>
<li><p><strong>자바9부터 deprecated되어 더이상 사용되지 않는다.</strong></p>
<pre><code class="language-java">public class FinalizeExample {
  public void finalize() {
      System.out.println(&quot;Finalize method is called.&quot;);
  }

  public static void main(String[] args) {
      FinalizeExample obj = new FinalizeExample();
      obj = null; // 객체에 대한 참조 제거
      System.gc(); // 가비지 컬렉터 실행 요청
  }
}</code></pre>
</li>
</ul>