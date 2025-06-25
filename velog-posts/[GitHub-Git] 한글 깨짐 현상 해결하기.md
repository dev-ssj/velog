<blockquote>
<p>깃 허브에 소스코드를 올리거나 내려받을 때 한글이 깨지는 현상이 종종 발생한다. 필자의 경우 properties파일에 단 주석들이 깨지는데 이 현상을 해결해보자.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/a6c30a7d-d912-4fbe-9a12-d2a7078d0399/image.png" /></p>
<ul>
<li>properties 파일의 한글이 깨짐</li>
</ul>
<h3 id="1-git에-연동한-프로젝트의-git-폴더로-이동">1. git에 연동한 프로젝트의 .git 폴더로 이동</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/12e6a1bf-b0a4-4a0b-853a-aa4d4c093397/image.png" /></p>
<ul>
<li>git에 연동한 프로젝트 폴더에 가보면 .git이라는 폴더가 있다.</li>
</ul>
<h3 id="2-config-파일-수정">2. config 파일 수정</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/7a4aeb76-9869-4b6b-87e9-c76e115d13dd/image.png" /></p>
<ul>
<li>.git 폴더 안에 config을 수정하자</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/4b0e00d1-5150-476c-ae17-675fa0fcd314/image.png" /></p>
<pre><code>[i18n]
    commitEncoding = utf-8
    logOutputEncoding = utf-8</code></pre><ul>
<li>맨 상단에 해당 내용을 입력하자.</li>
<li>인코딩을 utf-8 형식으로 하겠다는 내용이다. 이렇게 설정해두면 한글도 깨지지 않는다.</li>
</ul>