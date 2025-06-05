<h1 id="💡공공데이터-api-가져오기">💡공공데이터 API 가져오기</h1>
<p>개인프로젝트에서 공공데이터 API를 이용하여 데이터를 가져올 일이 생겼다.
특정시의 구와 동을 가져오고 싶어서, 행정동 데이터를 검색하기 시작했다.</p>
<h3 id="1-공공-데이터-포털httpswwwdatagokrindexdo에-접속-및-로그인">1. 공공 데이터 포털(<a href="https://www.data.go.kr/index.do)%EC%97%90">https://www.data.go.kr/index.do)에</a> 접속 및 로그인</h3>
<ul>
<li>공공 데이터 포털(<a href="https://www.data.go.kr/index.do)%EC%97%90%EC%84%9C">https://www.data.go.kr/index.do)에서</a> 다양한 OPEN API를 찾아볼 수 있다.</li>
</ul>
<h3 id="2-원하는-api-검색">2. 원하는 API 검색</h3>
<ul>
<li>내가 원하는 API는 행정동 정보 이므로 행정동 정보 API를 검색한다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/006555cf-8d42-42b1-bca4-bf5e58491296/image.png" /></li>
<li>데이터들을 CSV파일로도 제공하고 있고, 오픈 API로도 이용할 수 있다.</li>
<li>페이지 하단 데이터 정보에는 컬럼 정보와 데이터 미리보기가 있다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/2ee10d6a-efc5-4da0-bf2a-1c7f8fc24c64/image.png" /></li>
</ul>
<h3 id="3-활용-신청하기">3. 활용 신청하기</h3>
<ul>
<li><p>오픈 API  란을 누르면 활용신청 버튼이 뜬다. 클릭하자
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/fecc78d7-174f-43fe-9982-29ae21ed67b8/image.png" /></p>
</li>
<li><p>활용 신청을 하면 활용 목적 선택이 있는데 나는 웹사이트 개발용이므로 클릭해주고 신청을 하자.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/f4db7d7a-58b6-4cd5-bd81-f38a8d8c70c7/image.png" /></p>
</li>
<li><p>신청이 완료되면 활용에 1건이 떠있다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/8a7dd6a9-a633-4b67-b7d8-8dec4b94d27b/image.png" /></p>
</li>
</ul>
<h3 id="4-api-정보-확인">4. API 정보 확인</h3>
<ul>
<li>활용중인 API페이지에 들어가면 서비스정보에 url과 인증키가 적혀있다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/df90f350-b88a-4338-a553-5cc8180cacd7/image.png" /></li>
</ul>
<h3 id="5-api-인증키-설정">5. API 인증키 설정</h3>
<ul>
<li>더 아래로 내리면 활용신청 상세기능 정보가 있는데 여기서 API 테스트를 간단하게 해볼 수 있다.</li>
<li>인증키 설정 버튼을 눌러서 인증키를 설정해주자
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/a07ed292-9c09-40c4-8d7e-a40be89d5611/image.png" /></li>
<li>인증키 설정하는 두가지 부분이 나오는데, 내가 사용할 건 아래의 ApiKeyAuth2이다.</li>
<li>여기 value부분에 위에서 확인했던 두가지 인증키 중 아래에 있던 Decoding된 인증키를 붙여넣기해준다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/93e5e150-314a-4292-a505-fdda278c93ed/image.png" /></li>
</ul>
<h3 id="6-api-테스트">6. API 테스트</h3>
<ul>
<li>API 목록의 url을 클릭하면 api 테스트 해볼 수 있는 목록이 나온다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/400337fe-f772-41db-ba1a-0d8eaacec0ab/image.png" /></li>
</ul>
<blockquote>
<p>여기에는 api와 관련된 다양한 정보들을 확인할 수 있다.</p>
</blockquote>
<ol>
<li>GetMapping으로 api를 이용할 수 있으며</li>
<li>api의 url을 확인할 수 있고</li>
<li>page는 출력할 페이지, perPage는 한페이지당 출력할 데이터 갯수, returnType은 XML과 JSON중 어떤 형태로 출력할 건지 정하는 변수다.</li>
</ol>
<ul>
<li>OpenAPI 실행 준비 버튼을 누르면 API를 테스트 할 수 있다.</li>
</ul>
<h3 id="7-api-테스트-결과-확인">7. API 테스트 결과 확인</h3>
<ul>
<li>테스트가 제대로 되면 Request URL과 데이터들을 확인할 수 있다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/f60af0da-82f6-493e-9383-cf832cb7d215/image.png" /><blockquote>
<p>참고로 응답코드가 200이면 성공, 401이면 인증정보가 정확하지않음, 500이면 api서버 문제 발생이다.</p>
</blockquote>
</li>
</ul>
<h3 id="8-postman-테스트">8. PostMan 테스트</h3>
<ul>
<li>7에서 확인했던 RequestURL을 PostMan으로도 테스트해보면 제대로 출력되는 것을 확인할 수 있다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/c6a721d2-8ccc-452e-b06c-615293284ed7/image.png" /></li>
</ul>
<p>다음 편에서는 이 데이터들을 java로 출력하는 것을 해보겠다.</p>