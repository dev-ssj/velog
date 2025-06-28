<h1 id="💡custom-exception-만들기">💡Custom Exception 만들기</h1>
<blockquote>
<p><a href="https://velog.io/@dev_ssj/Spring-GlobalExceptionHandler%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%98%88%EC%99%B8%EC%B2%98%EB%A6%AC">이전 글</a>에서는 단순히 GlobalExceptionHandler로 기존에 존재하던 표준 예외들을 처리하는 로직을 만들었는데, 직접 예외 클래스를 정리하고 그에 맞는 예외처리 로직도 구현할 수 있다. 이것을 커스텀 예외처리라고 한다.</p>
</blockquote>
<hr />
<h2 id="📌-커스텀-예외가-필요한-이유">📌 커스텀 예외가 필요한 이유</h2>
<blockquote>
<p>표준 예외만 사용하는 경우에는 <code>RuntimeException</code>, <code>IllegalArgumentException</code>만 사용하면 <strong>예외의 의도를 코드만으로 파악하기 어렵고</strong>, HTTP 상태 코드나 메세지 등을 세분화하거나 <strong>통합 관리하기 어렵다</strong>는 단점이 있다. 이러한 단점들을 보완하기 위해 커스텀 예외가 필요하다.</p>
</blockquote>
<hr />
<h2 id="✅커스텀-예외-처리하기">✅커스텀 예외 처리하기</h2>
<h3 id="1-enum타입의-errorcode-작성하기">1. enum타입의 ErrorCode 작성하기</h3>
<pre><code class="language-java">@Getter
@AllArgsConstructor
public enum ErrorCode {
    INVALID_PASSWORD(401, &quot;비밀번호가 맞지않습니다&quot;),
    INTERNAL_ERROR(500, &quot;서버 내부 오류가 발생했습니다-ErrorCode Enum&quot;),
    USER_NOT_FOUND(404, &quot;해당 유저를 찾을 수 없습니다&quot;),
    DUPLICATE_USERID(1003, &quot;이미 존재하는 아이디입니다&quot;);

    private final int code;
    private final String message;
}</code></pre>
<ul>
<li>ErrorCode에서 내가 만들고 싶은 예외를 에러코드, 메시지를 enum으로 관리할 수 있다.</li>
</ul>
<h3 id="2-errorresponse-작성">2. ErrorResponse 작성</h3>
<pre><code class="language-java">@Getter
@AllArgsConstructor
public class ErrorResponse {
    private int code;   //상태코드
    private String message; //커스텀 에러 메시지(사용자용 메시지 ex.로그인 실패)
    private String detail;  //실제 에러 메세지(디버깅용 메시지 ex.Exception.getMessage())


    public static ErrorResponse of(ErrorCode errorCode) {
        return new ErrorResponse(errorCode.getCode(), errorCode.getMessage(), null);
    }

    public static ErrorResponse of(ErrorCode errorCode, String detail) {
        return new ErrorResponse(errorCode.getCode(), errorCode.getMessage(), detail);
    }
}</code></pre>
<ul>
<li><code>ErrorResponse</code>에는 정적 팩토리 메서드도 만들어줬는데, 상세 메시지 없이 간단한 오류 응답을 생성하는 메서드와 디버깅 정보까지 포함하여 응답을 생성하는 메서드 총 2가지를 만들었다.</li>
</ul>
<h3 id="3-globalexceptionhandler에-적용">3. GlobalExceptionHandler에 적용</h3>
<pre><code class="language-java">   @ExceptionHandler(RuntimeException.class)
    public ResponseEntity&lt;ErrorResponse&gt; handleRuntimeException(RuntimeException e){
        return ResponseEntity
                .status(HttpStatus.UNAUTHORIZED)
                .body(ErrorResponse.of(ErrorCode.INTERNAL_ERROR, e.getMessage()));
    }</code></pre>
<h3 id="4-testcontroller-작성">4. testController 작성</h3>
<pre><code class="language-java">@RestController
@RequestMapping(&quot;/exception&quot;)
public class TestController {

    @GetMapping
    public ResponseEntity&lt;ErrorResponse&gt; test() {
        throw new RuntimeException(&quot;글로벌 예외처리 테스트중~&quot;);
    }
}</code></pre>
<h3 id="5-결과-확인하기">5. 결과 확인하기</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/664e5e3f-9267-4d58-ade9-6dcb6582a2a0/image.png" /></p>
<ul>
<li><code>message</code> : ErrorCode에서 예외 생성 시 작성한 메시지</li>
<li><code>detail</code> : Controller에서 throw new로 예외를 넘길때 작성한 메시지</li>
</ul>
<hr />
<blockquote>
<p>이런식으로 커스텀 예외처리를 하게되면 <code>INVALID_PASSWORD</code>, <code>DUPLICATE_USERID</code> 등 <strong>도메인에 맞는 명확한 의미 전달</strong>이 가능하고, <strong>각 예외에 맞는 HTTP 상태코드, 메시지, 응답 구조</strong>를 제어할 수 있다는 이점이 있다. 또한, enum타입의 ErrorCode와 함께 사용하면 <strong>여러 응답을 일관되게 구성</strong>할 수 있으며 예외를 throw하는 코드와 예외를 처리하는 <strong>코드를 분리</strong>할 수 있다.</p>
</blockquote>