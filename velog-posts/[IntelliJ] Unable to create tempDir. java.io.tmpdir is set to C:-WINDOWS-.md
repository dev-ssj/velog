<p>여느때와 다름없이 개발을 하던 어느날....
아무생각 없이 Run을 눌렀다가 에러가 떴다..!</p>
<pre><code>org.springframework.context.ApplicationContextException: Unable to start web server
    at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.onRefresh(ServletWebServerApplicationContext.java:170) ~[spring-boot-3.5.4.jar:3.5.4]
    at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:621) ~[spring-context-6.2.9.jar:6.2.9]
    at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:146) ~[spring-boot-3.5.4.jar:3.5.4]
    at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:752) ~[spring-boot-3.5.4.jar:3.5.4]
    at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:439) ~[spring-boot-3.5.4.jar:3.5.4]
    at org.springframework.boot.SpringApplication.run(SpringApplication.java:318) ~[spring-boot-3.5.4.jar:3.5.4]
    at org.springframework.boot.SpringApplication.run(SpringApplication.java:1361) ~[spring-boot-3.5.4.jar:3.5.4]
    at org.springframework.boot.SpringApplication.run(SpringApplication.java:1350) ~[spring-boot-3.5.4.jar:3.5.4]
    at com.S1_K4.ForkMe_BE.ForkMeBeApplication.main(ForkMeBeApplication.java:14) ~[main/:na]
Caused by: org.springframework.boot.web.server.WebServerException: Unable to create tempDir. java.io.tmpdir is set to C:\WINDOWS\
    at org.springframework.boot.web.server.AbstractConfigurableWebServerFactory.createTempDir(AbstractConfigurableWebServerFactory.java:221) ~[spring-boot-3.5.4.jar:3.5.4]
    at org.springframework.boot.web.embedded.tomcat.TomcatServletWebServerFactory.getWebServer(TomcatServletWebServerFactory.java:201) ~[spring-boot-3.5.4.jar:3.5.4]
    at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.createWebServer(ServletWebServerApplicationContext.java:193) ~[spring-boot-3.5.4.jar:3.5.4]
    at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.onRefresh(ServletWebServerApplicationContext.java:167) ~[spring-boot-3.5.4.jar:3.5.4]
    ... 8 common frames omitted
Caused by: java.nio.file.AccessDeniedException: C:\WINDOWS\tomcat.8080.1425872368826919092
    at java.base/sun.nio.fs.WindowsException.translateToIOException(WindowsException.java:89) ~[na:na]
    at java.base/sun.nio.fs.WindowsException.rethrowAsIOException(WindowsException.java:103) ~[na:na]
    at java.base/sun.nio.fs.WindowsException.rethrowAsIOException(WindowsException.java:108) ~[na:na]
    at java.base/sun.nio.fs.WindowsFileSystemProvider.createDirectory(WindowsFileSystemProvider.java:521) ~[na:na]
    at java.base/java.nio.file.Files.createDirectory(Files.java:700) ~[na:na]
    at java.base/java.nio.file.TempFileHelper.create(TempFileHelper.java:134) ~[na:na]
    at java.base/java.nio.file.TempFileHelper.createTempDirectory(TempFileHelper.java:171) ~[na:na]
    at java.base/java.nio.file.Files.createTempDirectory(Files.java:1017) ~[na:na]
    at org.springframework.boot.web.server.AbstractConfigurableWebServerFactory.createTempDir(AbstractConfigurableWebServerFactory.java:215) ~[spring-boot-3.5.4.jar:3.5.4]
    ... 11 common frames omitted</code></pre><p> 생전 처음 보는 에러... 챗지피티에게 물어보니 
 <img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/66230ffd-dc3e-4456-abd0-ac4e8da783d3/image.png" />
임시 디렉터리를 못만들어서 에러가 뜬거라는데 난 이런거 한번도 만진적 없따고......(억울)
나같은 사람 없나 서치하다가 해결방법 찾았다!!!</p>
<p>Run -&gt; Edit Configuration 에서 환경변수 클릭
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/6c826283-0690-4c0a-95c2-d2239642efaa/image.png" /></p>
<p>밑에 저거 체크!
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/1da854b5-031b-48cd-b16a-8442f095b08d/image.png" /></p>
<p>하면 에러안뜬당ㅎㅂㅎ</p>