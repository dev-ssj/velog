<p>스프링부트 서버에서 엘라스틱 서치 의존성을 추가하고 기능을 구현하고 실행시켜 보니 아래와 같은 에러가 떴다.</p>
<pre><code class="language-bash">java.lang.NoSuchMethodError: 'double co.elastic.clients.elasticsearch.cluster.HealthResponse.activeShardsPercentAsNumber()'
    at org.springframework.boot.actuate.data.elasticsearch.ElasticsearchReactiveHealthIndicator.processResponse(ElasticsearchReactiveHealthIndicator.java:70)
    at org.springframework.boot.actuate.data.elasticsearch.ElasticsearchReactiveHealthIndicator.lambda$doHealthCheck$1(ElasticsearchReactiveHealthIndicator.java:49)
    at reactor.core.publisher.FluxMap$MapSubscriber.onNext(FluxMap.java:106)
    at reactor.core.publisher.MonoCompletionStage$MonoCompletionStageSubscription.apply(MonoCompletionStage.java:121)
    at reactor.core.publisher.MonoCompletionStage$MonoCompletionStageSubscription.apply(MonoCompletionStage.java:67)
    at java.base/java.util.concurrent.CompletableFuture.uniHandle(CompletableFuture.java:934)</code></pre>
<p>위 스프링 부트와 엘라스틱 서치 버전 호환문제 때문에 발생하는 에러라고 한다.
현재 스프링 부트 버전은 최신 버전인 3.5이고, build.gradle에서 엘라스틱서치 의존성은 아래와 같이 추가를 해줬었다.</p>
<pre><code class="language-bash">implementation 'org.springframework.boot:spring-boot-starter-data-elasticsearch'</code></pre>
<blockquote>
<h3 id="첫번째로-해본-방법">첫번째로 해본 방법</h3>
</blockquote>
<p>엘라스틱 서치는 스프링 부트 버전에 따라 권장되는 버전이 다르다.
스프링 부트 3.2 이상은 elasticsearch-java:8.12.x 이상으로, 
스프링 부트 3.3 이상은 Spring Boot 3.3.x 이상 → elasticsearch-java:8.13.x 이상의 버전이 권장된다.</p>
<p>현재 의존성에는 버전을 지정하지 않았으므로 버전을 명시적으로 지정해줬다.</p>
<pre><code class="language-bash">implementation 'org.springframework.boot:spring-boot-starter-data-elasticsearch'
implementation 'co.elastic.clients:elasticsearch-java:8.13.0' </code></pre>
<p>하지만 그럼에도 똑같은 에러가 떴다...ㅠㅠㅠㅠ
엄청난 삽질을 하다가 해결방법을 찾았다!</p>
<blockquote>
<h3 id="해결-방법">해결 방법</h3>
</blockquote>
<p>본래라면 위 의존성 추가시, 스프링 부트 3.5버전에 포함된 버전이 자동 적용이 되어야 하는데, 스프링 부트 3.5버전은 아직 정식 릴리즈 되지 않은 
Snapshot 릴리즈 버전이라 내부적으로 Spring Data Elasticsearch 5.4.x 이상과 연동되며 elasticsearch-java 클라이언트 8.14.x이 자동 적용 되야한다.
하지만 내가 현재 명시한 8.13버전이 스프링 부트 3.5버전과 호환이 되지 않아 에러가 뜨는것!</p>
<p>elasticsearch-java의 의존성을 8.14.0 이상으로 올리는 방법도 있지만, <strong>공식적으로 가장 권장되는 버전은 스프링부트 3.2.5 + elasticsearch-java:8.12.x 이므로 스프링 부트와 elasticsearch의 버전을 낮추도록 한다.</strong></p>
<pre><code class="language-bash">plugins {
    id 'org.springframework.boot' version '3.2.5' // 안정 버전
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-elasticsearch'
    implementation 'co.elastic.clients:elasticsearch-java:8.12.2' // Spring Boot 3.2와 호환
}</code></pre>
<p>이렇게 버전을 바꿨더니 에러가 사라졌다 야호 ٩(ˊᗜˋ*)و</p>