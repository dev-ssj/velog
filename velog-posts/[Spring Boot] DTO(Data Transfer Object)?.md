<h2 id="1-dto란">1. DTO란?</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/3c3cb9b8-1217-489d-924d-c519d0836b9d/image.png" /></p>
<blockquote>
<p>DTO(Data Transfer Object)는 계층 간 데이터 전달을 위한 객체로, 로직을 포함하지 않고 데이터를 담는 역할만 한다.</p>
</blockquote>
<h3 id="✅왜-dto를-써야-할까">✅왜 DTO를 써야 할까?</h3>
<p>*<em>1. 관심사 분리 *</em></p>
<ul>
<li>Entity는 DB와 직접 연결된 도메인 객체</li>
<li>DTO는 그와 별개로 프레젠테이션 계층(Controller, View) 또는 <strong>API 응답에 맞는 구조로 데이터를 전달</strong>.</li>
</ul>
<p><strong>2. 보안성과 캡슐화</strong></p>
<ul>
<li>Entity를 직접 외부에 노출할 경우 민감한 데이터(비밀번호 등)가 노출될 수 있음.</li>
<li>DTO는 <strong>필요한 필드만 선택적</strong>으로 포함할 수 있어 보안과 설계 측면에서 안전</li>
</ul>
<p><strong>3. API 응답 포맷 통제</strong></p>
<ul>
<li>JSON으로 직렬화 시, 필드명, 구조 등을 자유롭게 통제 가능</li>
<li>예 : 날짜 포맷 변경, 필드명 변환, 불필요한 정보 제거 등</li>
</ul>
<p><strong>4. 유지보수성 향상</strong></p>
<ul>
<li>Entity 구조가 바뀌어도 DTO를 통해 외부 API 포맷을 그대로 유지 가능</li>
<li>즉, <strong>내부 변경이 외부에 영향을 주지 않음</strong></li>
</ul>
<p><strong>5. 성능 최적화</strong></p>
<ul>
<li>Entity와 달리 DTO는 필요한 데이터만 선택해서 전달 -&gt; <strong>네트워크 트래픽 최소화</strong></li>
</ul>
<h2 id="2-dto-사용-예시">2. DTO 사용 예시</h2>
<h3 id="✅requestdto">✅RequestDto</h3>
<pre><code class="language-java">@Getter
public class UserReqestDto{
    private String name;
    private String email

   @Builder
    public UserRequestDto(String name, String email) {
        this.name = name;
        this.email = email;
    }

    //Entity -&gt; DTO 변환
    public User toEntity() {
        return User.builder()
            .name(name)
            .email(email)
            .build();
    }
}

/*
*  Controller 사용 예시
*/
UserRequestDto requestDto = UserRequestDto.builder()
    .name(&quot;홍길동&quot;)
    .email(&quot;aaa@aaa.com&quot;)
    .build();

User user = requestDto.toEntity();    //DTO -&gt; Entity로 변환</code></pre>
<h3 id="✅responsedto">✅ResponseDto</h3>
<pre><code class="language-java">@Getter
public class UserResponseDto{
    private Long id;
    private String name;
    private String email;

    @Bulider
    public UserResponseDto(Long id, String name, String email) {
    this.id = id;
    this.name = name;
    this.email = email;
    }

    //Entity -&gt; DTO 변환
    public static UserResponseDto from(User user){
    return UserResponseDto.builder()
        .id(user.getId())
        .name(user.getName())
        .email(user.getEmail())
        .build();
    }
}

/*
* Controller 사용 예시
*/
User user = userRepository.findById(1L).orElseThrow();
UserResponseDto responseDto = UserResponseDto.from(user);</code></pre>
<h2 id="3-요약">3. 요약</h2>
<table>
<thead>
<tr>
<th>구분</th>
<th>Entity</th>
<th>DTO</th>
</tr>
</thead>
<tbody><tr>
<td>목적</td>
<td>DB 연동, 비즈니스 로직</td>
<td>계층 간 데이터 전달</td>
</tr>
<tr>
<td>위험성</td>
<td>외부 노출 시 보안 문제</td>
<td>필요한 데이터만 노출</td>
</tr>
<tr>
<td>유연성</td>
<td>변경 시 외부 영향 있음</td>
<td>변경에 유연함</td>
</tr>
<tr>
<td>포함 데이터</td>
<td>모든 필드</td>
<td>필요한 필드만 선택</td>
</tr>
</tbody></table>