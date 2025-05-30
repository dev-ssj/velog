<h1 id="💡jpa를-이용해서-api를-db에-저장해보자">💡JPA를 이용해서 API를 DB에 저장해보자</h1>
<ul>
<li>이전까지는 JSON데이터 자체를 JAVA에서 출력하는 것을 했는데, API 데이터를 활용하기 위해서는 JSON데이터를 파싱하여 내가 원하는 형태로 JPA에 저장해보도록 하겠다.</li>
</ul>
<h2 id="📗entity-구현">📗Entity 구현</h2>
<ul>
<li>우선 <code>Entity</code>를 구현해 보도록 하겠다. API에서 제공하는 필드들은 여러가지지만 내가 원하는 필드들만 가져오도록 Entity를 설계했다.</li>
</ul>
<pre><code class="language-java">@Getter
@Entity
@NoArgsConstructor(access = lombok.AccessLevel.PROTECTED)
public class LocationDomain {
    @Id
    private Long locationCode;      //행정동코드
    @NotNull
    private String locationName;    //행정동명
    @NotNull
    private String revisedDate;     //개정날짜

    @Builder
    public LocationDomain(Long locationCode, String locationName, String revisedDate) {
        this.locationCode = locationCode;
        this.locationName = locationName;
        this.revisedDate = revisedDate;

    }
}</code></pre>
<blockquote>
<p>API 필드 중 <code>행정동 코드</code>, <code>행정동명</code>, <code>개정 날짜</code>만 가져오도록 Entity를 구성했다.</p>
</blockquote>
<h2 id="📗repository-구현">📗Repository 구현</h2>
<pre><code class="language-@Repository">public interface LocationRepository extends JpaRepository&lt;LocationDomain, Long&gt; {
}</code></pre>
<h2 id="📗service-구현">📗Service 구현</h2>
<pre><code class="language-java">@Service
public class ApiExplorer implements CommandLineRunner {

    @Autowired
    private LocationRepository locationRepository;

    @Override
    public void run(String... args) throws Exception {
        int page = 1;
        boolean hasMoreData = true;
        ObjectMapper objectMapper = new ObjectMapper(); // JSON 파싱용
        //중복 방지용 set
        Set&lt;Long&gt; uniqueCodes = new HashSet&lt;&gt;();
        //최종 데이터 저장용 list
        List&lt;LocationDomain&gt; DataList = new ArrayList&lt;&gt;();


        while (hasMoreData) {
            // 1. URL 생성
            StringBuilder urlBuilder = new StringBuilder(&quot;https://api.odcloud.kr/api/15136373/v1/uddi:84f86747-7012-4d74-b4c6-9d5068312ac4&quot;);
            urlBuilder.append(&quot;?&quot;).append(URLEncoder.encode(&quot;page&quot;, StandardCharsets.UTF_8)).append(&quot;=&quot;).append(page);
            urlBuilder.append(&quot;&amp;&quot;).append(URLEncoder.encode(&quot;perPage&quot;, StandardCharsets.UTF_8)).append(&quot;=&quot;).append(&quot;10000&quot;);
            urlBuilder.append(&quot;&amp;&quot;).append(URLEncoder.encode(&quot;returnType&quot;, StandardCharsets.UTF_8)).append(&quot;=&quot;).append(&quot;JSON&quot;);
            urlBuilder.append(&quot;&amp;&quot;).append(URLEncoder.encode(&quot;serviceKey&quot;, StandardCharsets.UTF_8))
                    .append(&quot;=taoPZY395PqfMgnAZMcUnm/fxpYemRwZ8E30EfFyoDRAaQwbmou/fcvoq6DpixOwN0WGOBUrEdxCnk1gO17RAQ==&quot;);

            URL url = new URL(urlBuilder.toString());
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            conn.setRequestMethod(&quot;GET&quot;);
            conn.setRequestProperty(&quot;Content-type&quot;, &quot;application/json&quot;);
            System.out.println(&quot;Response code: &quot; + conn.getResponseCode());

            // 전달받은 데이터를 BufferedReader 객체로 저장.
            StringBuilder sb = new StringBuilder();
            try (BufferedReader rd = (conn.getResponseCode() &gt;= 200 &amp;&amp; conn.getResponseCode() &lt; 300)
                    ? new BufferedReader(new InputStreamReader(conn.getInputStream()))
                    : new BufferedReader(new InputStreamReader(conn.getErrorStream()))) {

                String line;
                while ((line = rd.readLine()) != null) {
                    sb.append(line);
                }
                // JSON 파싱
                JsonNode jsonNode = objectMapper.readTree(sb.toString());
                JsonNode dataArray = jsonNode.get(&quot;data&quot;);

                if (dataArray == null || !dataArray.isArray() || dataArray.isEmpty()) {
                    hasMoreData = false; // 더 이상 데이터가 없으면 종료
                    System.out.println(&quot;✅모든 데이터 불러오기 완료&quot;);
                } else {
                    for (JsonNode node : dataArray) {
                        long locationCode = node.path(&quot;행정동코드&quot;).asLong();
                        String revisedDate = node.path(&quot;개정일자&quot;).asText(null);

                        //데이터 중복 제거 후 저장
                        if (uniqueCodes.add(locationCode)) {
                            LocationDomain data = LocationDomain.builder()
                                    .locationCode(locationCode)
                                    .locationName(node.path(&quot;행정동명&quot;).asText(null))
                                    .revisedDate(node.path(&quot;개정일자&quot;).asText(null))
                                    .build();

                            DataList.add(data);
                        }
                    }

                    System.out.println(&quot;페이지: &quot; + page + &quot;, 체크된 데이터: &quot; + DataList.size());
                    page++;  // Go to the next page
                }
            }
        }
        if (!DataList.isEmpty()) {
            locationRepository.saveAll(DataList);
            System.out.println(&quot;✅ 데이터 저장 완료&quot;);
        }
        for (Object item : DataList) {
            System.out.println(item);
        }
    }
}</code></pre>
<ul>
<li>이전에 java로 json객체를 받았을 때 코드와 거의 비슷하다.</li>
<li><code>@Autowired</code>로 locationRepository를 주입받고, 내가 가져온 API에는 중복되는 데이터가 많아서 중복을 방지하는 HashSet객체, 최종 데이터를 저장하는 List 객체를 지정한다.</li>
<li>각 페이지당 데이터를 읽고, for문을 이용하여 행정동코드와 개정일자를 저장하고, 중복된 데이터가 없으면 <code>Bulider패턴</code>을 이용하여 행정동 코드, 행정동명, 개정일자를 <code>LocationDomain</code>에 저장한다.</li>
<li>모든 데이터 읽기가 끝나면, <code>saveAll()</code>을 이용하여 데이터를 DB에 저장한다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/0d6eb8e6-0217-4a33-9c5f-499cc0da81c6/image.png" />
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/34a5ff85-fc22-46c6-8780-e52397541a13/image.png" /></li>
<li>데이터가 제대로 들어간 것을 확인할 수 있다.</li>
</ul>