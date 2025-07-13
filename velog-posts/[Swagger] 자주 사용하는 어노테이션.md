<h2 id="✅swagger-문서화-구성-예시">✅Swagger 문서화 구성 예시</h2>
<h3 id="📌-1-컨트롤러-전체-설명-tag">📌 1. 컨트롤러 전체 설명: <code>@Tag</code></h3>
<pre><code class="language-java">@Tag(name = &quot;User API&quot;, description = &quot;회원 관련 기능 (회원가입, 로그인 등)&quot;)
@RestController
@RequestMapping(&quot;/api/v1/users&quot;)
public class UserController {
    ...
}</code></pre>
<hr />
<h3 id="📌-2-메서드-설명-operation">📌 2. 메서드 설명: <code>@Operation</code></h3>
<pre><code class="language-java">@Operation(summary = &quot;회원가입&quot;, description = &quot;사용자의 정보를 받아 회원가입을 처리합니다.&quot;)
@PostMapping(&quot;/signup&quot;)
public ResponseEntity&lt;CommonResponse&gt; signup(@RequestBody @Valid UserSignupRequest request) {
    ...
}</code></pre>
<hr />
<h3 id="📌-3-파라미터-설명-parameter">📌 3. 파라미터 설명: <code>@Parameter</code></h3>
<pre><code class="language-java">@Operation(summary = &quot;회원 단건 조회&quot;)
@GetMapping(&quot;/{userId}&quot;)
public ResponseEntity&lt;UserResponse&gt; getUserById(
        @Parameter(description = &quot;회원 ID&quot;, example = &quot;1&quot;)
        @PathVariable Long userId) {
    ...
}</code></pre>
<hr />
<h3 id="📌-4-dto-필드-설명-schema">📌 4. DTO 필드 설명: <code>@Schema</code></h3>
<pre><code class="language-java">public class UserSignupRequest {

    @Schema(description = &quot;사용자 이메일&quot;, example = &quot;ssj@naver.com&quot;)
    private String email;

    @Schema(description = &quot;비밀번호&quot;, example = &quot;1234abcd!!&quot;)
    private String password;

    @Schema(description = &quot;닉네임&quot;, example = &quot;순주짱&quot;)
    private String nickname;

    // getter, setter 생략
}</code></pre>
<hr />
<h2 id="✅-swagger-ui-적용-결과-요약">✅ Swagger UI 적용 결과 요약</h2>
<ul>
<li>각 API에 <strong>한눈에 어떤 기능인지 요약</strong>이 보임 (<code>summary</code>)</li>
<li>상세 설명, 예시값까지 있어서 <strong>프론트와 협업하기 쉬움</strong></li>
<li>DTO도 Swagger에서 <strong>자동으로 문서화됨</strong></li>
</ul>
<hr />
<h2 id="➕-실무-팁">➕ 실무 팁</h2>
<ul>
<li>응답 코드 설명은 <code>@ApiResponse</code>로 따로 문서화 가능</li>
<li>권한 인증이 필요한 API는 Swagger에 헤더 정보 설정 가능 (<code>@SecurityRequirement</code>)</li>
<li><code>@Hidden</code>을 사용하면 Swagger에서 감출 수도 있음 (내부용 API 등)</li>
</ul>
<hr />
<h2 id="✅-실무-swagger-적용-예제--usercontrollerjava">✅ 실무 Swagger 적용 예제 – <code>UserController.java</code></h2>
<pre><code class="language-java">java
복사편집
package com.example.api.user;

import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.Parameter;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.security.SecurityRequirement;
import io.swagger.v3.oas.annotations.tags.Tag;
import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@Tag(name = &quot;회원 API&quot;, description = &quot;회원가입 및 사용자 정보 조회 관련 API&quot;)
@RestController
@RequestMapping(&quot;/api/v1/users&quot;)
public class UserController {

    @Operation(
        summary = &quot;회원가입&quot;,
        description = &quot;이메일, 비밀번호, 닉네임으로 새로운 회원을 등록합니다.&quot;,
        responses = {
            @ApiResponse(responseCode = &quot;201&quot;, description = &quot;회원가입 성공&quot;),
            @ApiResponse(responseCode = &quot;400&quot;, description = &quot;잘못된 요청&quot;, content = @Content)
        }
    )
    @PostMapping(&quot;/signup&quot;)
    public ResponseEntity&lt;String&gt; signup(
            @RequestBody UserSignupRequest request) {
        // 회원가입 처리 로직
        return ResponseEntity.status(201).body(&quot;회원가입 완료&quot;);
    }

    @Operation(
        summary = &quot;회원 정보 조회&quot;,
        description = &quot;회원 ID로 사용자 정보를 조회합니다.&quot;,
        security = @SecurityRequirement(name = &quot;Bearer Authentication&quot;),
        responses = {
            @ApiResponse(responseCode = &quot;200&quot;, description = &quot;회원 조회 성공&quot;),
            @ApiResponse(responseCode = &quot;404&quot;, description = &quot;해당 회원 없음&quot;)
        }
    )
    @GetMapping(&quot;/{userId}&quot;)
    public ResponseEntity&lt;UserResponse&gt; getUserById(
            @Parameter(description = &quot;회원 ID&quot;, example = &quot;1&quot;)
            @PathVariable Long userId) {

        // 조회 로직 예시
        UserResponse response = new UserResponse(&quot;s@naver.com&quot;, &quot;길동&quot;);
        return ResponseEntity.ok(response);
    }

    // --- DTOs ---

    @Schema(name = &quot;UserSignupRequest&quot;, description = &quot;회원가입 요청 객체&quot;)
    public static class UserSignupRequest {

        @Schema(description = &quot;이메일&quot;, example = &quot;s56@naver.com&quot;)
        @Email
        @NotBlank
        private String email;

        @Schema(description = &quot;비밀번호&quot;, example = &quot;1234abcd!!&quot;)
        @NotBlank
        private String password;

        @Schema(description = &quot;닉네임&quot;, example = &quot;짱&quot;)
        @NotBlank
        private String nickname;

        // Getters/Setters
        public String getEmail() { return email; }
        public void setEmail(String email) { this.email = email; }

        public String getPassword() { return password; }
        public void setPassword(String password) { this.password = password; }

        public String getNickname() { return nickname; }
        public void setNickname(String nickname) { this.nickname = nickname; }
    }

    @Schema(name = &quot;UserResponse&quot;, description = &quot;회원 정보 응답 객체&quot;)
    public static class UserResponse {

        @Schema(description = &quot;이메일&quot;, example = &quot;s3434@naver.com&quot;)
        private String email;

        @Schema(description = &quot;닉네임&quot;, example = &quot;짱&quot;)
        private String nickname;

        public UserResponse(String email, String nickname) {
            this.email = email;
            this.nickname = nickname;
        }

        public String getEmail() { return email; }
        public String getNickname() { return nickname; }
    }
}
</code></pre>
<hr />
<h2 id="✅-swagger-화면에서-확인되는-내용">✅ Swagger 화면에서 확인되는 내용</h2>
<ul>
<li>그룹명: <code>회원 API</code></li>
<li><code>POST /api/v1/users/signup</code><ul>
<li>요약: 회원가입</li>
<li>설명: 사용자 정보를 받아 새 계정을 생성</li>
<li>요청 본문: 필드별 설명, 예시값 포함</li>
</ul>
</li>
<li><code>GET /api/v1/users/{userId}</code><ul>
<li>보안: JWT 토큰 필요 (Authorize 버튼)</li>
<li>응답 필드 설명 포함</li>
</ul>
</li>
</ul>