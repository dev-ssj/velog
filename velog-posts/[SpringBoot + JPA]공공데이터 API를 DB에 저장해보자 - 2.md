<h1 id="💡api-데이터를-java로-가져와서-바꿔보자">💡API 데이터를 JAVA로 가져와서 바꿔보자</h1>
<blockquote>
<p>📌API는 보통 <code>JSON</code> 또는 <code>XML</code>형태로 제공하기 때문에 활용하려면 API데이터를 파싱하여 JAVA데이터로 받아야한다.
내가 원하는 대로 사용하기 위해 차근차근 API를 JSON형태로 JAVA로 가져오고, 그걸 파싱하고 마지막으로 JPA를 이용하여 DB에 저장해 보도록 하겠다.</p>
</blockquote>
<h2 id="📗1-api데이터를-json형태로-java로-가져오자">📗1. API데이터를 JSON형태로 JAVA로 가져오자</h2>
<ul>
<li>내가 사용하려는 공공데이터 API는 예시 코드가 없어서 다른 데이터들의 예시  코드를 나에게 맞게 수정했다.</li>
</ul>
<pre><code class="language-java">public class test {
    public static void main(String[] args) throws IOException {
        // 1. URL을 만들기 위한 StringBuilder.
        StringBuilder urlBuilder = new StringBuilder(&quot;https://api.odcloud.kr/api/15136373/v1/uddi:84f86747-7012-4d74-b4c6-9d5068312ac4&quot;); /*URL*/
        // 2. 오픈 API의요청 규격에 맞는 파라미터 생성, 발급받은 인증키.
        urlBuilder.append(&quot;?&quot;).append(URLEncoder.encode(&quot;page&quot;, StandardCharsets.UTF_8)).append(&quot;=&quot;).append(URLEncoder.encode(&quot;1&quot;, StandardCharsets.UTF_8)); /*페이지 번호*/
        urlBuilder.append(&quot;&amp;&quot;).append(URLEncoder.encode(&quot;perPage&quot;, StandardCharsets.UTF_8)).append(&quot;=&quot;).append(URLEncoder.encode(&quot;1&quot;, StandardCharsets.UTF_8)); /*한 페이지 결과 수*/
        urlBuilder.append(&quot;&amp;&quot;).append(URLEncoder.encode(&quot;returnType&quot;, StandardCharsets.UTF_8)).append(&quot;=&quot;).append(URLEncoder.encode(&quot;JSON&quot;, StandardCharsets.UTF_8)); /*XML 또는 JSON*/
        urlBuilder.append(&quot;&amp;&quot;).append(URLEncoder.encode(&quot;serviceKey&quot;, StandardCharsets.UTF_8)).append(&quot;=서비스키&quot;); /*Service Key*/
        // 3. URL 객체 생성.
        URL url = new URL(urlBuilder.toString());
        // 4. 요청하고자 하는 URL과 통신하기 위한 Connection 객체 생성.
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        // 5. 통신을 위한 메소드 SET.
        conn.setRequestMethod(&quot;GET&quot;);
        // 6. 통신을 위한 Content-type SET.
        conn.setRequestProperty(&quot;Content-type&quot;, &quot;application/json&quot;);
        // 7. 통신 응답 코드 확인.
        System.out.println(&quot;Response code: &quot; + conn.getResponseCode());
        // 8. 응답코드가 정상(200~300)이면 전달받은 데이터를 BufferedReader 객체로 저장.
        BufferedReader rd;
        if(conn.getResponseCode() &gt;= 200 &amp;&amp; conn.getResponseCode() &lt;= 300) {
            rd = new BufferedReader(new InputStreamReader(conn.getInputStream()));
        } else {
            rd = new BufferedReader(new InputStreamReader(conn.getErrorStream()));
        }
        // 9. 저장된 데이터를 라인별로 읽어 StringBuilder 객체로 저장.
        StringBuilder sb = new StringBuilder();
        String line;
        while ((line = rd.readLine()) != null) {
            sb.append(line);
        }
        // 10. 객체 해제.
        rd.close();
        conn.disconnect();
        // 11. 전달받은 데이터 확인.
        System.out.println(sb.toString());
        System.out.println(url);
    }
}</code></pre>
<ul>
<li>이전 1편에서 테스트했던 URL을 확인해보면 <h4 id="httpsapiodcloudkrapi15136373v1uddi84f86747-7012-4d74-b4c6-9d5068312ac4page1perpage1returntypejsonservicekey서비스키"><a href="https://api.odcloud.kr/api/15136373/v1/uddi:84f86747-7012-4d74-b4c6-9d5068312ac4?page=1&amp;perPage=1&amp;returnType=JSON&amp;serviceKey=%EC%84%9C%EB%B9%84%EC%8A%A4%ED%82%A4">https://api.odcloud.kr/api/15136373/v1/uddi:84f86747-7012-4d74-b4c6-9d5068312ac4?page=1&amp;perPage=1&amp;returnType=JSON&amp;serviceKey=서비스키</a></h4>
<ul>
<li>이렇게 구성되어 있는데, 이를 토대로 코드 2번처럼 url을 구현해준다.</li>
<li>Parameters는 <code>page</code>가 1이고, <code>Perpage</code>가 1, <code>returnTtype</code>이 JSON이므로
➡️JSON 형태로 각 페이지마다 1개의 데이터를 출력하며, 첫번째 페이지 데이터를 가져온다. 라는 뜻이 된다.</li>
<li>응답코드는 200~300 즉, 정상일때만 객체를 저장한다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/4e8f7933-5c2e-46f2-8045-2719052c0327/image.png" /></li>
</ul>
</li>
<li>출력 결과를 보면 응답코드가 출력되고 1페이지에 1개의 데이터가 출력된다. 출력된 url을 보면 url이 잘 전달된것도 확인할 수 있다.</li>
</ul>
<h2 id="📗2-api데이터를-json으로-전체-데이터-출력을-해보자">📗2. API데이터를 JSON으로 전체 데이터 출력을 해보자.</h2>
<blockquote>
<p>위에서는 필수 Parameter로 <code>page</code>와 <code>perPage</code>가 들어간다. 즉, 한번 출력할때 한 페이지 씩만 출력 된다는건데, API데이터에서 내가 원하는 데이터들을 찾으려면 전체 데이터 출력을 해야하므로 <code>While</code>문을 이용해서 전체 데이터 출력을 해보자</p>
</blockquote>
<pre><code class="language-java">public class ApITest {
    public static void main(String[] args) throws IOException, IOException {
        int page = 1;
        boolean hasMoreData = true;
        ObjectMapper objectMapper = new ObjectMapper(); // JSON 파싱용

        System.out.println(&quot;🚀 ApiExplorer started!&quot;);

        while (hasMoreData) {
            // 1. URL 생성
            StringBuilder urlBuilder = new StringBuilder(&quot;https://api.odcloud.kr/api/15136373/v1/uddi:84f86747-7012-4d74-b4c6-9d5068312ac4&quot;);
            urlBuilder.append(&quot;?&quot;).append(URLEncoder.encode(&quot;page&quot;, StandardCharsets.UTF_8)).append(&quot;=&quot;).append(page);
            urlBuilder.append(&quot;&amp;&quot;).append(URLEncoder.encode(&quot;perPage&quot;, StandardCharsets.UTF_8)).append(&quot;=&quot;).append(&quot;5&quot;); // perPage 값 증가
            urlBuilder.append(&quot;&amp;&quot;).append(URLEncoder.encode(&quot;returnType&quot;, StandardCharsets.UTF_8)).append(&quot;=&quot;).append(&quot;JSON&quot;);
            urlBuilder.append(&quot;&amp;&quot;).append(URLEncoder.encode(&quot;serviceKey&quot;, StandardCharsets.UTF_8))
                    .append(&quot;=taoPZY395PqfMgnAZMcUnm/fxpYemRwZ8E30EfFyoDRAaQwbmou/fcvoq6DpixOwN0WGOBUrEdxCnk1gO17RAQ==&quot;);

            URL url = new URL(urlBuilder.toString());
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            conn.setRequestMethod(&quot;GET&quot;);
            conn.setRequestProperty(&quot;Content-type&quot;, &quot;application/json&quot;);

            System.out.println(&quot;Response code: &quot; + conn.getResponseCode());

            BufferedReader rd = (conn.getResponseCode() &gt;= 200 &amp;&amp; conn.getResponseCode() &lt;= 300)
                    ? new BufferedReader(new InputStreamReader(conn.getInputStream()))
                    : new BufferedReader(new InputStreamReader(conn.getErrorStream()));

            StringBuilder sb = new StringBuilder();
            String line;
            while ((line = rd.readLine()) != null) {
                sb.append(line);
            }
            rd.close();
            conn.disconnect();

            // JSON 파싱
            JsonNode jsonNode = objectMapper.readTree(sb.toString()); //JSON문자열을 JsonNode로 구문분석
            JsonNode dataArray = jsonNode.get(&quot;data&quot;); //구문 분석된 JSON문자열에서 data필드를 추출하여 jsonNode로 저장

            //모든 페이지 출력시에는 dataArray == null || !dataArray.isArray() || dataArray.isEmpty()로 조건문 주기
            if (page == 4) {
                hasMoreData = false; // 더 이상 데이터가 없으면 종료
                System.out.println(&quot;✅ All data fetched.&quot;);
            } else {
                // 데이터 처리 (여기서 필터링 및 중복 제거 가능)
                System.out.println(&quot;Fetched page: &quot; + page + &quot;, Records: &quot; + dataArray.size());
                System.out.println(&quot;==================================================&quot;);
                System.out.println(dataArray);
                page++; // 다음 페이지로
            }
        }
    }
}</code></pre>
<ul>
<li><p>1번의 코드와 달라진건 <code>ObjectMapper</code>를 사용한다는 것과 API 처리 코드가 <code>while</code>문으로 감싸져 있다는 것이다.</p>
</li>
<li><p><code>ObjectMapper</code>란 JAVA의 <code>jackson</code>라이브러리에서 제공하는 클래스로, <code>JSON</code>데이터 변환을 처리하여 <code>JSON</code>객체를 <code>java</code>변수로 쉽게 바꾸고(<code>역직렬화</code>) 그 반대로도(<code>직렬화</code>)바꿀수 있게 해준다.</p>
</li>
<li><p><code>JsonNode dataArray = jsonNode.get(&quot;data&quot;);</code> 여기서말하는 data필드는 아래의 JSON 결과에서 되는 data를 말한다. API의 구문에 따라 변경하면 된다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/73ea65b2-83b3-466e-a26a-2dff563dcb96/image.png" /></p>
</li>
<li><p>true로 값을 준 <code>hasMoreData</code>를 이용하여 <code>while</code>문을 실행 시키고, 데이터가 있다면 <code>page</code>의 값만 1씩 증가시킨 채 계속 반복문을 돌린다.</p>
</li>
<li><p>모든 페이지 출력 시에는 주석처럼 <code>dataArray == null || !dataArray.isArray() || dataArray.isEmpty()</code>를 조건문으로 주면 되는데, 가져온 API의 데이터가 너무 많아서 3페이지까지만 출력하도록 설정해 놓았다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/6ca274c9-6851-44a1-8342-4f5b6c186e15/image.png" /></p>
</li>
<li><p>출력 결과를 확인하면 3페이지 까지 모든 데이터가 출력된다.</p>
</li>
<li><p>다음 편에서는 드디어 JPA를 이용하여 API를 DB에 넣는 것 까지 해보도록 하겠다.</p>
</li>
</ul>