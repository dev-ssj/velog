<h1 id="💡elk-stack-로그-모니터링-적용하기">💡ELK Stack 로그 모니터링 적용하기</h1>
<blockquote>
<p><a href="https://velog.io/@dev_ssj/Monitoring-%EB%A1%9C%EA%B7%B8-%EC%B2%98%EB%A6%AC%EC%99%80-ELK-%EC%8A%A4%ED%83%9D">이전 글</a>에서 언급한 Elasticsearch, Logstash, Kibana 세 기술을 이용하여 로그 모니터링 대시보드를 만들어 보도록 하자.</p>
</blockquote>
<hr />
<h2 id="1-resource-폴더에-logback-springxml-파일-추가">1. resource 폴더에 logback-spring.xml 파일 추가</h2>
<pre><code class="language-xml">&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot; ?&gt;
&lt;configuration&gt;

&lt;!--    로그 파일이 저장될 경로 변수 (/logs) --&gt;
    &lt;property
            name=&quot;LOG_PATH&quot;
            value=&quot;logs&quot;
    /&gt;

    &lt;!-- 파일 로그 출력 패턴: 색상 없이 정형화된 포맷. ELK에 파싱하기 좋음 (ELK 연동용) --&gt;
    &lt;property
            name=&quot;FILE_PATTERN&quot;
            value=&quot;%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] [%-3level] %logger{5} - %msg%n&quot;
    /&gt;

    &lt;!-- ==========================
        로그 파일 설정 (ELK 연동용)
        ========================== --&gt;
    &lt;appender name=&quot;FILE&quot; class=&quot;ch.qos.logback.core.rolling.RollingFileAppender&quot;&gt;  &lt;!-- RollingFileAppender: 날짜별로 로그 파일 자동으로 분할 생성 --&gt;
        &lt;file&gt;${LOG_PATH}/app.log&lt;/file&gt;
        &lt;rollingPolicy class=&quot;ch.qos.logback.core.rolling.TimeBasedRollingPolicy&quot;&gt;
            &lt;fileNamePattern&gt;${LOG_PATH}/app-%d{yyyy-MM-dd}.log&lt;/fileNamePattern&gt;    &lt;!-- (app-2025-06-28.log와 같은 형식으로 파일 이름 지정 --&gt;
            &lt;maxHistory&gt;10&lt;/maxHistory&gt;  &lt;!-- : 10일치까지만 파일 보관(자동 삭제) --&gt;
        &lt;/rollingPolicy&gt;
        &lt;encoder&gt;    &lt;!-- ELK 연동 전용 로그 포맷 --&gt;
            &lt;Pattern&gt;${FILE_PATTERN}&lt;/Pattern&gt;
        &lt;/encoder&gt;
    &lt;/appender&gt;

    &lt;!-- 콘솔(터미널)에 출력할 때 사용할 패턴 (색상 적용) --&gt;
    &lt;property
            name=&quot;CONSOLE_PATTERN&quot;
            value=&quot;%d{yyyy-MM-dd HH:mm:ss.SSS} %magenta([%thread]) %highlight([%-3level]) %logger{5} - %msg %n&quot;
    /&gt;

    &lt;!-- 터미널에 출력하는 Appender 설정 - 개발 환경 전용 --&gt;
    &lt;appender
            name=&quot;STDOUT&quot;
            class=&quot;ch.qos.logback.core.ConsoleAppender&quot;&gt;
        &lt;encoder&gt;
            &lt;Pattern&gt;${CONSOLE_PATTERN}&lt;/Pattern&gt;
        &lt;/encoder&gt;
    &lt;/appender&gt;

    &lt;!-- 콘솔 로그 비동기 처리(성능 최적화, 버퍼링)
          아래 ASYNC는 위 STDOUT 출력을 비동기로 최적화(성능 향상) --&gt;

    &lt;appender
            name=&quot;ASYNC&quot;
            class=&quot;ch.qos.logback.classic.AsyncAppender&quot;&gt;
        &lt;appender-ref ref=&quot;STDOUT&quot;/&gt;
    &lt;/appender&gt;



    &lt;!-- ==========================
             패키지별 로그 레벨 지정
         ========================== --&gt;
    &lt;!--(INFO, WARN, ERROR도 모두 출력)--&gt;
    &lt;!-- 특정 패키지(여기서는 org.boot.backend5project)만 따로 레벨 지정 가능
         additive=&quot;false&quot;면, 해당 패키지 로그는 상위(root)에 전달되지 않음 --&gt;
    &lt;logger
            name=&quot;org.boot.backend5project&quot;
            level=&quot;DEBUG&quot;
            additive=&quot;false&quot; &gt;
        &lt;appender-ref ref=&quot;STDOUT&quot;/&gt;
    &lt;/logger&gt;


&lt;!-- 글로벌 루트 로그 설정 --&gt;
    &lt;root level=&quot;INFO&quot;&gt; &lt;!-- 애플리케이션 전체 기본 로그 레벨 설정을 INFO로 설정--&gt;
        &lt;appender-ref ref=&quot;ASYNC&quot;/&gt; &lt;!-- 콘솔(터미널) 로그 비동기 처리 --&gt;
        &lt;appender-ref ref=&quot;FILE&quot;/&gt;  &lt;!-- 파일 로그 기록(ELK 연동) --&gt;
    &lt;/root&gt;
&lt;/configuration&gt;</code></pre>
<hr />
<h2 id="2-logstashyml">2. logstash.yml</h2>
<pre><code class="language-bash">http.host: &quot;0.0.0.0&quot;
xpack.monitoring.elasticsearch.hosts: [ &quot;http://elasticsearch:9200&quot; ]</code></pre>
<hr />
<h2 id="3-logstashconf">3. logstash.conf</h2>
<pre><code class="language-bash"> input {
    file {
      path =&gt; &quot;/logs/app.log&quot;            #Docker 볼륨으로 마운트된 로그 파일 경로
      start_position =&gt; &quot;beginning&quot;        #Logstash가 처음부터 읽도록 설정
      sincedb_path =&gt; &quot;/dev/null&quot;          # 로그 변경사항 감지 초기화용
      type =&gt; &quot;backend3&quot;
    }
}

filter {
#아래 규칙에서 자동으로 키워드로 형성되어 키바나에서 확인 가능
  grok {
    match =&gt; {
      &quot;message&quot; =&gt; &quot;%{TIMESTAMP_ISO8601:timestamp} \[%{DATA:thread}\] \[%{LOGLEVEL:level}\s*\] %{JAVACLASS:logger} - %{GREEDYDATA:log}&quot;
    }
  }


   # 로그가 AOP 로그인지 식별 
   # 로그에 AOP_LOG 또는 OAuth2_Log 라는 문자열이 포함되면 log_type를 추가
   # Kibana에서 log_type.keyword로 필터링 시 사용 가능
   if &quot;AOP_LOG&quot; in [message] {
     mutate {
       add_field =&gt; { &quot;log_type&quot; =&gt; &quot;aop&quot; }
     }
   }
   else if &quot;OAuth2_LOG&quot; in [message]{
     mutate {
       add_field =&gt; { &quot;log_type&quot; =&gt; &quot;OAuth2&quot; }
     }
   }


  date {
    match =&gt; [&quot;timestamp&quot;, &quot;yyyy-MM-dd HH:mm:ss.SSS&quot;]
    timezone =&gt; &quot;Asia/Seoul&quot;
    target =&gt; &quot;@timestamp&quot;
  }
}

output {
   # log 전용이고 검색 엔진이랑은 상관없은 검색엔진은 logstash 안씀
   #엘라스틱서치에서 해당 이름의 인덱스가 없으면 자동 생성함
  elasticsearch {
    hosts =&gt; [&quot;http://elasticsearch:9200&quot;]
    index =&gt; &quot;spring-logs-%{+YYYY.MM.dd}&quot;
  }
  stdout { codec =&gt; rubydebug } # 디버깅용
}
</code></pre>
<hr />
<h2 id="4docker-composemonitoringyml-수정">4.docker-compose.monitoring.yml 수정</h2>
<pre><code class="language-yml">    services:

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    ports:
      - &quot;9090:9090&quot;
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml # [설정파일]
      - ./volumes/prometheus:/prometheus          # [데이터 볼륨]
    networks:
      - prod_server

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: unless-stopped
    ports:
      - &quot;3000:3000&quot;
    depends_on:
      - prometheus
    volumes:
      - ./volumes/grafana:/var/lib/grafana  # [데이터 볼륨]
    networks:
      - prod_server

  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.12.0
    container_name: elasticsearch
    environment:
      - node.name=es01
      - discovery.type=single-node
      - xpack.security.enabled=false
    ulimits:
      memlock:
        soft: -1
        hard: -1
    ports:
      - &quot;9200:9200&quot;
      - &quot;9300:9300&quot;
    volumes:
      - ./volumes/esdata:/usr/share/elasticsearch/data
    networks:
      - prod_server

  kibana:
    image: docker.elastic.co/kibana/kibana:8.12.0
    container_name: kibana
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
      - SERVER_SSL_ENABLED=false
    ports:
      - &quot;5601:5601&quot;
    volumes:
      - ./volumes/kibana-data:/usr/share/kibana/data
    depends_on:
      - elasticsearch
    networks:
      - prod_server

  logstash:
    image: docker.elastic.co/logstash/logstash:8.12.0
    container_name: logstash
    ports:
      - &quot;5044:5044&quot;  # For beats (optional)
      - &quot;5000:5000&quot;  # TCP input
    volumes:
      - ./logstash/logstash.conf:/usr/share/logstash/pipeline/logstash.conf #logstash 설정파일
      - ./logstash/logstash.yml:/usr/share/logstash/config/logstash.yml:ro  # &lt;-- 이 줄 추가!
      - ../../logs:/logs #로그 볼륨
    depends_on:
      - elasticsearch
    networks:
      - prod_server


networks:
  prod_server:
    external: true
</code></pre>
<hr />
<h2 id="5-docker-compose--f-docker-composemonitoringyml-up--d">5. docker-compose -f docker-compose.monitoring.yml up -d<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/8f39c81d-8e57-4506-b85f-518b4d08bbca/image.png" /></h2>
<hr />
<h2 id="6-httplocalhost9200_catindicesv-로-접속">6. <a href="http://localhost:9200/_cat/indices?v">http://localhost:9200/_cat/indices?v</a> 로 접속<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/9545f098-7abb-4c06-bceb-6dd9c44e0279/image.png" /></h2>
<ul>
<li><a href="http://localhost:9200/_cat/indices?v">http://localhost:9200/_cat/indices?v</a> : Elasticsearch의 인덱스 목록을 조회하는 API</li>
</ul>
<h3 id="6-1-참고-docker-logs-logstash-명령어로-로그-내역-확인-가능">6-1 참고) docker logs logstash 명령어로 로그 내역 확인 가능</h3>
<pre><code class="language-bash">      &quot;@version&quot; =&gt; &quot;1&quot;,
        &quot;logger&quot; =&gt; &quot;o.e.b.s.j.JwtTokenFilter&quot;,
          &quot;type&quot; =&gt; &quot;backend3&quot;,
    &quot;@timestamp&quot; =&gt; 2025-06-27T02:13:40.209Z,
         &quot;level&quot; =&gt; &quot;INFO&quot;,
        &quot;thread&quot; =&gt; &quot;http-nio-8080-exec-8&quot;,
     &quot;timestamp&quot; =&gt; &quot;2025-06-27 11:13:40.209&quot;,
       &quot;message&quot; =&gt; &quot;2025-06-27 11:13:40.209 [http-nio-8080-exec-8] [INFO] o.e.b.s.j.JwtTokenFilter - ❌ 토큰 없음 또는 유효하지 않음: null\r&quot;,
         &quot;event&quot; =&gt; {
        &quot;original&quot; =&gt; &quot;2025-06-27 11:13:40.209 [http-nio-8080-exec-8] [INFO] o.e.b.s.j.JwtTokenFilter - ❌ 토큰 없음 또는 유효하지 않음: null\r&quot;
    }
}
{
          &quot;host&quot; =&gt; {
        &quot;name&quot; =&gt; &quot;536437bfba64&quot;
    },
           &quot;log&quot; =&gt; {
        &quot;file&quot; =&gt; {
            &quot;path&quot; =&gt; &quot;/logs/app.log&quot;
        }
    },
      &quot;@version&quot; =&gt; &quot;1&quot;,
        &quot;logger&quot; =&gt; &quot;o.e.b.s.j.JwtTokenFilter&quot;,
          &quot;type&quot; =&gt; &quot;backend3&quot;,
    &quot;@timestamp&quot; =&gt; 2025-06-27T02:13:45.212Z,
         &quot;level&quot; =&gt; &quot;INFO&quot;,
        &quot;thread&quot; =&gt; &quot;http-nio-8080-exec-9&quot;,
     &quot;timestamp&quot; =&gt; &quot;2025-06-27 11:13:45.212&quot;,
       &quot;message&quot; =&gt; &quot;2025-06-27 11:13:45.212 [http-nio-8080-exec-9] [INFO] o.e.b.s.j.JwtTokenFilter - ❌ 토큰 없음 또는 유효하지 않음: null\r&quot;,
         &quot;event&quot; =&gt; {
        &quot;original&quot; =&gt; &quot;2025-06-27 11:13:45.212 [http-nio-8080-exec-9] [INFO] o.e.b.s.j.JwtTokenFilter - ❌ 토큰 없음 또는 유효하지 않음: null\r&quot;
    }
}
{
          &quot;host&quot; =&gt; {
        &quot;name&quot; =&gt; &quot;536437bfba64&quot;
    },
           &quot;log&quot; =&gt; {
        &quot;file&quot; =&gt; {
            &quot;path&quot; =&gt; &quot;/logs/app.log&quot;
        }
    },
      &quot;@version&quot; =&gt; &quot;1&quot;,
        &quot;logger&quot; =&gt; &quot;o.e.b.s.j.JwtTokenFilter&quot;,
          &quot;type&quot; =&gt; &quot;backend3&quot;,
    &quot;@timestamp&quot; =&gt; 2025-06-27T02:13:50.210Z,
         &quot;level&quot; =&gt; &quot;INFO&quot;,
        &quot;thread&quot; =&gt; &quot;http-nio-8080-exec-10&quot;,
     &quot;timestamp&quot; =&gt; &quot;2025-06-27 11:13:50.210&quot;,
        &quot;thread&quot; =&gt; &quot;http-nio-8080-exec-1&quot;,
     &quot;timestamp&quot; =&gt; &quot;2025-06-27 11:13:55.210&quot;,
       &quot;message&quot; =&gt; &quot;2025-06-27 11:13:55.210 [http-nio-8080-exec-1] [INFO] o.e.b.s.j.JwtTokenFilter - ❌ 토큰 없음 또는 유효하지 않음: null\r&quot;,
         &quot;event&quot; =&gt; {
        &quot;original&quot; =&gt; &quot;2025-06-27 11:13:55.210 [http-nio-8080-exec-1] [INFO] o.e.b.s.j.JwtTokenFilter - ❌ 토큰 없음 또는 유효하지 않음: null\r&quot;
    }
}</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/addf54c4-9a6f-46be-915f-a435df4982ae/image.png" /></p>
<ul>
<li><strong>docker logs logstash</strong> :  logs폴더 내의 app.log에 있는 로그 내역들을 다불러오는 명령어</li>
</ul>
<hr />
<h2 id="7-httplocalhost5601-접속--→-kibana">7. <a href="http://localhost:5601/">http://localhost:5601/</a> 접속  → Kibana<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/c11a4bf3-3d75-4bf6-8953-204be657aadb/image.png" /></h2>
<hr />
<h2 id="8-메뉴--discover--create-data-view">8. [메뉴] &gt; [Discover] &gt; [Create data view]<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/83019002-2d49-4688-a557-8ef4af496c51/image.png" /></h2>
<hr />
<h2 id="9-name과-index-pattern-작성-후-save">9. name과 index pattern 작성 후 Save <img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/564188b5-e0d1-4357-b6c2-13c0265095e9/image.png" /></h2>
<hr />
<h2 id="10-kibana에서-로그-시각화하기">10. Kibana에서 로그 시각화하기</h2>
<h3 id="10-1-메뉴--visuallize-library--create-new-visuallization">10-1. [메뉴] &gt; [Visuallize Library] &gt; [Create new visuallization]<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/05c0b9fd-9469-4d26-b7f5-c86a45e5dcf6/image.png" /></h3>
<h3 id="10-2-7번에서-만든-data-view-지정">10-2. 7번에서 만든 data view 지정<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/a202927f-f7c1-4157-b9bf-00ec2e931a60/image.png" /></h3>
<h3 id="10-3-horizontal-axis-vertical-axis-breakdown-설정">10-3. Horizontal axis, Vertical axis, Breakdown 설정<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/91ae8ef9-273e-4fc0-a369-f2e81c49b67e/image.png" /></h3>
<h3 id="10-4-선택-add-filter-설정-→-설정한-로그만-확인-가능">10-4. (선택) Add filter 설정 → 설정한 로그만 확인 가능<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/1c19f318-c302-4433-bfe0-168e1b33d213/image.png" /></h3>
<hr />
<h2 id="11-대시보드-확인">11. 대시보드 확인<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/a610660a-83a1-48bd-b452-c00807a3d134/image.png" /><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/c9dee7d4-86cc-48b5-b4c6-8fd657cf8744/image.png" /></h2>
<ul>
<li>로그 상세 내역도 확인 가능</li>
</ul>
<hr />
<h1 id="📝elk-동작-흐름-정리">📝ELK 동작 흐름 정리</h1>
<ol>
<li>애플리케이션이 <code>logback-spring.xml</code>에 따라 로그 생성</li>
<li>로그는 <code>logs/app.log</code> 파일에 계속 누적됨</li>
<li>Logstash가 <code>logstash.conf</code>의 <code>input</code>에서 해당 로그를 감지</li>
<li>Logstash가 로그 파싱 후, <code>Elasticsearch</code>로 전송 → <strong>localhost:9200</strong></li>
<li><code>logstash.yml</code> 설정에 따라 엘라스틱 서치에 저장된 데이터를 Kibana로 시각화 → <strong>localhost:5601</strong></li>
</ol>