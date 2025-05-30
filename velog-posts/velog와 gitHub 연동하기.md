<blockquote>
<p>velog에 글을 작성하면 gitHub에 자동으로 커밋되게 만들고 싶었다.
찾아보니 연동하는 방법이 있어서 연동해봄!</p>
</blockquote>
<h3 id="1-git-repository-생성">1. Git Repository 생성</h3>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/ab8c5f9a-364b-4e2a-b21a-f2963e460ce7/image.png" /></p>
<h3 id="2-update_blogyml-파일-생성">2. update_blog.yml 파일 생성</h3>
<h4 id="1-createing-a-new-file-클릭">1) createing a new file 클릭</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/dc3f26e6-4c23-4a72-98ae-6fece07ef97c/image.png" /></p>
<ul>
<li>파일경로 : <code>.github/workflows/update_blog.yml</code></li>
<li><code>update_blog.yml</code> 파일 작성<pre><code class="language-yml">name: Update Blog Posts

</code></pre>
</li>
</ul>
<p>on:
  push:
      branches:
        - [브랜치명] # 또는 워크플로우를 트리거하고 싶은 브랜치 이름
  workflow_dispatch:    # 수동 실행 트리거 추가
  schedule:
    - cron: '0 0 * * *' # 매일 자정에 실행</p>
<p>jobs:
  update_blog:
    runs-on: ubuntu-latest</p>
<pre><code>steps:
- name: Checkout
  uses: actions/checkout@v2

- name: Set time zone
  run: |
    sudo ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
    echo &quot;Asia/Seoul&quot; | sudo tee /etc/timezone
    date

- name: Push changes
  run: |
    git config --global user.name 'github-actions[bot]'
    git config --global user.email 'github-actions[bot]@users.noreply.github.com'
    git push https://${{ secrets.GH_PAT }}@github.com/[연동할깃허브아이디]/velog.git

- name: Set up Python
  uses: actions/setup-python@v2
  with:
    python-version: '3.x'

- name: Install dependencies
  run: |
    pip install feedparser gitpython

- name: Run script
  run: python scripts/update_blog.py</code></pre><pre><code>  &gt; 📌 **수동 실행 트리거 추가**
  위 코드대로 파일을 추가하면 매일 자정에 자동 커밋이 되는데, `workflow_dispatch:`를 추가하게 되면 필요할 때마다 수동으로 커밋이 가능하다. 


  #### 2) commit하기
![](https://velog.velcdn.com/images/dev_ssj/post/f3f68f3f-428a-41ff-ad40-04ba4573e730/image.png)

### 3. update_blog.py 파일 생성
 #### 1) velog 리포지토리 -&gt; Add file -&gt; Create new file
![](https://velog.velcdn.com/images/dev_ssj/post/ca084e8b-e2bd-4384-86b6-6c23a751f026/image.png)

 - 파일 경로 : `scripts/update_blog.py`
 - `update_blog.py` 파일 작성
```python
import feedparser
import git
import os

# 벨로그 RSS 피드 URL
# example : rss_url = 'https://api.velog.io/rss/@rimgosu'
rss_url = 'https://api.velog.io/rss/@[벨로그아이디]'

# 깃허브 레포지토리 경로
repo_path = '.'

# 'velog-posts' 폴더 경로
posts_dir = os.path.join(repo_path, 'velog-posts')

# 'velog-posts' 폴더가 없다면 생성
if not os.path.exists(posts_dir):
    os.makedirs(posts_dir)

# 레포지토리 로드
repo = git.Repo(repo_path)

# RSS 피드 파싱
feed = feedparser.parse(rss_url)

# 각 글을 파일로 저장하고 커밋
for entry in feed.entries:
    # 파일 이름에서 유효하지 않은 문자 제거 또는 대체
    file_name = entry.title
    file_name = file_name.replace('/', '-')  # 슬래시를 대시로 대체
    file_name = file_name.replace('\\', '-')  # 백슬래시를 대시로 대체
    # 필요에 따라 추가 문자 대체
    file_name += '.md'
    file_path = os.path.join(posts_dir, file_name)

    # 파일이 이미 존재하지 않으면 생성
    if not os.path.exists(file_path):
        with open(file_path, 'w', encoding='utf-8') as file:
            file.write(entry.description)  # 글 내용을 파일에 작성

        # 깃허브 커밋
        repo.git.add(file_path)
        repo.git.commit('-m', f'Add post: {entry.title}')

# 변경 사항을 깃허브에 푸시
repo.git.push()</code></pre><h4 id="2-commit-하기">2) commit 하기</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/06b5db11-d322-4b53-b9a2-37e423508dcf/image.png" /></p>
<h3 id="4-personal-access-tokens-생성하기">4. Personal access tokens 생성하기</h3>
<h4 id="1-github-계정---setting-클릭">1) gitHub 계정 -&gt; Setting 클릭</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/3cc50831-52e2-45c8-87db-4648a6362432/image.png" /></p>
<h4 id="2-왼쪽-메뉴-하단-developer-settings-클릭">2) 왼쪽 메뉴 하단 Developer settings 클릭</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/acffeeb6-f7c8-48a8-9628-cb39ebcae3c6/image.png" /></p>
<h4 id="3-personal-access-tokens---tokensclassic---generate-new-token---generate-new-tokenclassic">3) Personal access tokens -&gt; Tokens(classic) -&gt; Generate new Token -&gt; Generate new token(classic)</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/905a1756-65f2-432f-8107-0897b4e7f65a/image.png" /></p>
<h4 id="4-토큰-이름-작성과-유효기간-설정--repo-workflow-체크-후-generate-token-클릭">4) 토큰 이름 작성과 유효기간 설정 &amp; repo, workflow 체크 후 Generate Token 클릭</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/ecd7d9ee-3ad7-41d7-8c21-921618899d5d/image.png" />
<code>Expiration</code>은 해당 토큰의 유효기간을 뜻하는데, 기본으로 30일이 선택되어있다. 
필자는 <code>No expiration</code>로 변경했다.</p>
<h4 id="5-토큰-복사">5) 토큰 복사</h4>
<ul>
<li>이 창을 벗어나면 이후에는 <strong>토큰 확인이 불가능</strong>하다. 꼭! 토큰 복사해두기!
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/4d88aabe-da1c-4519-b788-0b59f3396630/image.png" /></li>
</ul>
<h4 id="6-velog-리포지토리---setting---secrets-and-variables---actions---new-repository-secret">6) velog 리포지토리 -&gt; setting -&gt; secrets and variables -&gt; Actions -&gt; new Repository secret</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/135b210c-2129-4adc-8353-9d8593822f33/image.png" /></p>
<h4 id="7-name과-복사한-토큰-붙여넣고-add-secret-클릭">7) name과 복사한 토큰 붙여넣고 Add secret 클릭</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/348cc580-e201-43ce-aa2f-5f99060a6940/image.png" /></p>
<h3 id="5-리포지토리에-외부-권한-부여하기">5. 리포지토리에 외부 권한 부여하기</h3>
<h4 id="1-git-리포지토리---setting---actions---general에서-체크된-4가지-항목-선택">1) git 리포지토리 -&gt; Setting -&gt; Actions -&gt; General에서 체크된 4가지 항목 선택</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/c60a2eac-d3a8-46c8-847d-6bda7ba832af/image.png" /></p>
<h3 id="6-연동-확인">6. 연동 확인</h3>
<blockquote>
<p>자정이 될 때까지 기다릴수도 있지만 <code>update_blog.yml</code>에 수동 실행 트리거를 추가했으므로 수동으로 확인해 보도록 하자.</p>
</blockquote>
<h4 id="1-git-리포지토리---action---update-blog-posts---run-workflow-클릭">1) git 리포지토리 -&gt; Action -&gt; Update Blog Posts -&gt; Run workflow 클릭</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/54d2bf09-ee2d-46f5-8fc8-7908f0f74378/image.png" /></p>
<ul>
<li>연동이 잘 된걸 확인할 수 있다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/885a3894-1437-44ce-943a-86d51c2689da/image.png" /></li>
</ul>