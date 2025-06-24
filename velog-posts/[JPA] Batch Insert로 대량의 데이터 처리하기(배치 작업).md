<h1 id="💡batch-insert로-대량의-데이터-처리하기">💡Batch Insert로 대량의 데이터 처리하기</h1>
<blockquote>
<p>Batch Insert란 대량의 데이터를 한 번의 데이터베이스 작업으로 차리하는 기법이다. 
실무에서 대량의 데이터를 넣게 될 때, 몇만건 이상이 된다면 데이터를 저장하는 과정이 매우 오래 걸리게 된다. 이 때, JPA의 saveAll 메서드가 아닌, jdbcTemplate의 batch insert를 직접 구현하는 방법으로 데이터를 좀 더 빠르고 유연하게 넣을 수 있다. </p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/137c16b0-feef-4d7a-99b9-bc78839469ee/image.png" /></p>
<hr />
<h2 id="🤔jpa-identity-전략에서-배치-작업이-어려운-이유">🤔JPA identity 전략에서 배치 작업이 어려운 이유</h2>
<ul>
<li>JPA에서 Identity 전략은 <strong>DB가 기본 키를 직접 생성하는 방식</strong>이기 때문에 JPA가 엔티티를 DB에 즉시 insert 해야만 ID 값을 알 수 있다.</li>
<li>그래서 엔티티를 1개씩 insert → ID 받고 → 다음 insert → 또 ID 받고...→ 이렇게 반복적으로 DB 접근이 필요하므로 <strong>배치 insert가 불가능하거나 매우 비효율적이다</strong></li>
</ul>
<hr />
<h2 id="📦배치작업batch-processing">📦배치작업(Batch Processing)</h2>
<h3 id="🔍의미">🔍의미</h3>
<blockquote>
<p><strong>대량의 데이터를 처리하기 위해 여러 insert/update/delete를 일정 단위로 묶어 처리하는 방식</strong></p>
</blockquote>
<h3 id="📌특징">📌특징</h3>
<ul>
<li>주로 insert, update, delete를 효율적으로 처리</li>
<li>쿼리는 여러개지만, 한번에 묶어서 DB에 보내기 때문에 네트워크 비용이 적다</li>
<li>각 INSERT가 독립적으로 동작하므로 에러 하나가 전체를 망치지 않음</li>
</ul>
<hr />
<h2 id="🏭-벌크-작업-bulk-operation">🏭 벌크 작업 (Bulk Operation)</h2>
<h3 id="🔍의미-1">🔍의미</h3>
<blockquote>
<p><strong>한 번의 JPQL/SQL 쿼리로 여러 행을 입력, 수정, 삭제하는 작업</strong></p>
</blockquote>
<h3 id="📌특징-1">📌특징</h3>
<ul>
<li>모든 데이터를 한 쿼리로 처리할 수 있을 때 사용</li>
<li>쿼리 조건이 동일하거나 간단한 경우에 사용</li>
<li>쿼리가 한개이기 때문에 DB입장에서 가장 빠르다.</li>
<li>처리 대상이 간단한 데이터 구조이거나 조건이 일괄적일 때 유리</li>
</ul>
<hr />
<h2 id="✅jpa의-saveall">✅JPA의 saveAll</h2>
<h4 id="applicationproperties">application.Properties</h4>
<pre><code class="language-bash">spring.jpa.properties.hibernate.jdbc.batch_size=1000
spring.jpa.properties.hibernate.order_inserts=true</code></pre>
<ul>
<li>JPA에서 batch insert를 사용할 때 Hibernate가 최적화된 SQL을 생성하도록 돕는 옵션이다.</li>
<li>이 설정을 제대로 활용하려면 반드시 <strong>JPA에서 persist() 또는 saveAll() 같은 JPA 저장 방식을 사용해야 반영</strong>된다. → JdbcTemplate 사용시에는 전혀 연관 없음!</li>
</ul>
<h4 id="boardservice">BoardService</h4>
<pre><code class="language-java">private final EntityManager em;
    @Transactional
    public void boardSaveAll(List&lt;Board&gt; boardList){
        long start = System.currentTimeMillis();

         //1000개마다 flush로 DB에 실제 INSERT하고, clear로 메모리 비우고 비영속 객체로 만드는것.
        //천 개를 하나씩 INSERT SQL로 쌓아뒀다가 전송
        for(int i = 0; i&lt; boardList.size(); i++){
            em.persist(boardList.get(i));    //엔티티를 영속성 컨텍스트에만 저장.
            if(i%1000 == 0) {
                em.flush();                    //DB INSERT SQL은 여기서 전송됨!
                em.clear();                    //영속성 컨텍스트에 존재하는 모든 엔티티를 비영속 객체로 만듬.
            }
        }
        long end = System.currentTimeMillis();
        System.out.println(&quot;JPA Board saveAll 저장 소요 시간(ms) : &quot; + (end - start) );
    }</code></pre>
<ul>
<li>매번 persist()를 호출하고, 그 결과를 영속성 컨텍스트에 쌓아두기 때문에 느리다.</li>
<li>1000개의 쿼리를 모았다가 한꺼번에 보내는것 같지만 그게 아님!</li>
<li><strong>persist()를 1000번 호출</strong> → 객체를 1000개 영속화 (등록)</li>
<li><strong>flush() 1번 호출</strong> → 1000개 insert SQL을 한꺼번에 전송</li>
</ul>
<h4 id="실행-결과">실행 결과 <img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/7982893b-4e85-42b5-8bba-39dc21777608/image.png" /></h4>
<blockquote>
<p>JPA에서 여러 개 단위의 Insert를 제공하는 메서드인 saveAll은 List 객체를 담아 사용하지만, <strong>등록할 데이터 수만큼 Insert 쿼리가 나간다</strong>. 대량의 데이터 기준으로 saveAll 메서드를 사용하게 될 경우 JPA의 특성인 쓰기 지연으로 인해 아주 오랜 시간 동안 INSERT 쿼리가 발생하고 있는 것을 볼 수 있다.</p>
</blockquote>
<hr />
<h2 id="✅jpa-batch-insert">✅JPA Batch Insert</h2>
<pre><code class="language-bash">spring:
  datasource:        
        url: ~/rewriteBatchedStatements=true&amp;profileSQL=true

 jpa:
    hibernate:
      ddl-auto: create 
    properties:
      hibernate:
        show-sql: true
        jdbc:
          batch_size: 100
        order_inserts: true 
        order_updates: true</code></pre>
<blockquote>
<p><strong>saveAll에서 많은 행의 Insert 쿼리를 줄일 수 있는 방법으로, order_inserts 속성이 있다</strong>. 다음과 같이 BatchSize와 order_inserts의 사용을 명시해 준다면 BatchSize의 수만큼 insert할때 하나의 쿼리로 묶이는 것을 볼 수 있다.</p>
</blockquote>
<h2 id="✅jdbc-template-batch-insert★">✅JDBC Template Batch Insert★</h2>
<h4 id="batchrepository">BatchRepository</h4>
<pre><code class="language-java"> public void batchInsert(List&lt;BoardDTO&gt; boardDTO){

        String sql = &quot;INSERT INTO board (title, content, user_id, created_date, updated_date,batchkey) VALUES (?, ?, ?, ?, ?,?)&quot;;
        jdbcTemplate.batchUpdate(sql, new BatchPreparedStatementSetter() {  //sql을 JDBC batch모드로 실행
            @Override
            public void setValues(PreparedStatement ps, int i) throws SQLException {
                BoardDTO dto = boardDTO.get(i);
                ps.setString(1, dto.getTitle());
                ps.setString(2, dto.getContent());
                ps.setLong(3, dto.getUser_id());
                ps.setString(4, String.valueOf(dto.getCreated_date()));
                ps.setString(5, String.valueOf(dto.getUpdated_date()));
                ps.setString(6, dto.getBatchkey());

            }
            @Override
            public int getBatchSize() {
                return boardDTO.size();
            }   //1000Q번 INSERT 반복
        });
    }</code></pre>
<p>위 코드는 내부적으로 아래 과정을 자동으로 처리한다.</p>
<ul>
<li><strong>PreparedStatement 한 번 생성</strong></li>
<li>setValues()를 1000번 호출해서 1<strong>000개의 데이터 묶음을 addBatch()로 쌓음</strong></li>
<li>executeBatch()를 호출해서 <strong>한 번에 DB로 전송</strong></li>
</ul>
<h4 id="boardservice-1">BoardService</h4>
<pre><code class="language-java">@Transactional
    public void batchSaveBoard(List&lt;BoardDTO&gt; boardDTOList) {
        Long start = System.currentTimeMillis();

        int batchsize = 1000; //한번에 처리할 배치 크기
        for (int i = 0; i &lt; boardDTOList.size(); i+=batchsize) { //i는 1000씩 증가
            //전체 데이터를 1000개씩 잘라서 배치리스트에 담습니다.

            int end = Math.min(boardDTOList.size(), i+batchsize); //두개의 숫자중에 작은 숫자를 반환
            List&lt;BoardDTO&gt; batchList = boardDTOList.subList(i, end);

            //전체 데이터에서 1000씩 작업을 하는데 마지막 데이터가 1000개가 안될수도있으니
            //Math.min()으로 전체 크기를 넘지 않게 마지막 인덱스를 계산해서 작업합니다.


            //내가 넣은 데이터만 엘라스틱서치에 동기화하기 위해 uuid 생성
            String batchKey = UUID.randomUUID().toString();
            for (BoardDTO dto : batchList) {
                dto.setBatchkey(batchKey);
            }


            // 1. MySQL로 INSERT
            batchRepository.batchInsert(batchList);
        }

        Long end = System.currentTimeMillis();
        log.info(&quot;[BOARD][BATCH] 전체 저장 소요 시간(ms): {}&quot;, (end - start));
    }</code></pre>
<h4 id="결과">결과<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/37392f8d-2fe2-4842-beb1-c8acf7e4b7c0/image.png" /></h4>
<blockquote>
<p>어떠한 상황에서도 Batch Insert를 수행할 수 있는 방법으로는 S<strong>pring JDBC에서 제공하는 Batch Insert가 있다.</strong> 이를 사용할 경우 IDENTITY 전략일 경우에도 정상적으로 Insert 쿼리가 한방에 나가는 것을 볼 수 있다.</p>
</blockquote>