<h3 id="🗂️-1-저장소-초기화-및-클론">🗂️ 1. 저장소 초기화 및 클론</h3>
<table>
<thead>
<tr>
<th>명령어</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><code>git init</code></td>
<td>로컬 Git 저장소 초기화</td>
</tr>
<tr>
<td><code>git clone &lt;URL&gt;</code></td>
<td>원격 저장소 복제</td>
</tr>
</tbody></table>
<br />

<h3 id="🔍-2-상태-확인-및-로그">🔍 2. 상태 확인 및 로그</h3>
<table>
<thead>
<tr>
<th>명령어</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><code>git status</code></td>
<td>변경된 파일/스테이징 상태 확인</td>
</tr>
<tr>
<td><code>git log</code></td>
<td>커밋 히스토리 확인</td>
</tr>
<tr>
<td><code>git log --oneline</code></td>
<td>간략한 커밋 목록 확인</td>
</tr>
</tbody></table>
<br />

<h3 id="📝-3-파일-추가--커밋">📝 3. 파일 추가 &amp; 커밋</h3>
<table>
<thead>
<tr>
<th>명령어</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><code>git add &lt;파일명&gt;</code></td>
<td>특정 파일 스테이징</td>
</tr>
<tr>
<td><code>git add .</code></td>
<td>모든 변경 파일 스테이징</td>
</tr>
<tr>
<td><code>git commit -m &quot;메시지&quot;</code></td>
<td>커밋 생성</td>
</tr>
</tbody></table>
<br />

<h3 id="🌱-4-브랜치">🌱 4. 브랜치</h3>
<table>
<thead>
<tr>
<th>명령어</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><code>git branch</code></td>
<td>현재 브랜치 목록 확인</td>
</tr>
<tr>
<td><code>git branch &lt;이름&gt;</code></td>
<td>새 브랜치 생성 (이동은 안 함)</td>
</tr>
<tr>
<td><code>git checkout -b &lt;이름&gt;</code></td>
<td>새 브랜치 생성 + 이동</td>
</tr>
<tr>
<td><code>git switch -c &lt;이름&gt;</code></td>
<td>새 브랜치 생성 + 이동 (추천)</td>
</tr>
<tr>
<td><code>git switch &lt;이름&gt;</code></td>
<td>브랜치 이동</td>
</tr>
<tr>
<td><code>git merge &lt;브랜치&gt;</code></td>
<td>다른 브랜치를 현재 브랜치에 병합</td>
</tr>
</tbody></table>
<br />

<h3 id="☁️-5-원격-저장소와-연동">☁️ 5. 원격 저장소와 연동</h3>
<table>
<thead>
<tr>
<th>명령어</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><code>git remote -v</code></td>
<td>연결된 원격 저장소 보기</td>
</tr>
<tr>
<td><code>git push origin &lt;브랜치&gt;</code></td>
<td>브랜치 원격 푸시</td>
</tr>
<tr>
<td><code>git push -u origin &lt;브랜치&gt;</code></td>
<td>최초 푸시 + 추적 설정</td>
</tr>
<tr>
<td><code>git pull</code></td>
<td>원격 변경 사항 받아오기</td>
</tr>
<tr>
<td><code>git fetch</code></td>
<td>원격 변경사항만 받아오기 (병합은 안 함)</td>
</tr>
</tbody></table>
<br />

<h3 id="🔧-6-기타-유용한-명령어">🔧 6. 기타 유용한 명령어</h3>
<table>
<thead>
<tr>
<th>명령어</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><code>git diff</code></td>
<td>변경된 코드 비교 보기</td>
</tr>
<tr>
<td><code>git reset HEAD &lt;파일&gt;</code></td>
<td>add한 파일을 스테이징 취소</td>
</tr>
<tr>
<td><code>git rm --cached &lt;파일&gt;</code></td>
<td>Git에서 추적만 제거 (파일은 유지)</td>
</tr>
<tr>
<td><code>git stash</code></td>
<td>현재 변경사항 임시 저장</td>
</tr>
<tr>
<td><code>git stash pop</code></td>
<td>저장한 변경사항 복원</td>
</tr>
<tr>
<td><code>git rebase -i HEAD~n</code></td>
<td>최근 n개 커밋 합치기/수정하기</td>
</tr>
</tbody></table>
<br />

<h3 id="✅-7-내가-자주-쓰는-명령어">✅ 7. 내가 자주 쓰는 명령어</h3>
<table>
<thead>
<tr>
<th>명령어</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><code>git fetch origin</code></td>
<td>원격 저장소의 최신 브랜치/커밋/태그 정보를 <strong>로컬 저장소에만 가져옴</strong> (코드 변경 없음)</td>
</tr>
<tr>
<td><code>git switch -c &lt;브랜치&gt;</code></td>
<td><strong>새 브랜치를 생성하고 이동</strong> (<code>checkout -b</code>와 동일, Git 2.23+ 권장)</td>
</tr>
<tr>
<td><code>git checkout -b &lt;브랜치&gt;</code></td>
<td>새 브랜치 생성 후 이동 (기존 방식)</td>
</tr>
<tr>
<td><code>git add .</code></td>
<td>모든 변경 파일을 <strong>스테이징 영역에 추가</strong></td>
</tr>
<tr>
<td><code>git commit -m &quot;메시지&quot;</code></td>
<td>스테이징된 내용을 <strong>하나의 커밋으로 기록</strong></td>
</tr>
<tr>
<td><code>git push</code></td>
<td>현재 브랜치의 커밋을 <strong>원격 저장소로 푸시</strong><br />(<code>git push -u origin &lt;브랜치&gt;</code> 최초 1회 필요)</td>
</tr>
<tr>
<td><code>git pull</code></td>
<td>원격 저장소의 변경사항을 <strong>가져오고 병합(Merge)</strong></td>
</tr>
<tr>
<td><code>git rebase &lt;브랜치&gt;</code></td>
<td>현재 브랜치의 커밋을 <code>&lt;브랜치&gt;</code> 뒤에 <strong>다시 쌓아서 최신화</strong> (히스토리 깔끔하게 유지)</td>
</tr>
<tr>
<td><code>git rebase -i HEAD~3</code></td>
<td>최근 3개의 커밋을 <strong>인터랙티브하게 수정/합치기</strong></td>
</tr>
<tr>
<td><code>git rebase --continue</code></td>
<td>충돌 해결 후 rebase <strong>계속 진행</strong></td>
</tr>
<tr>
<td><code>git rebase --abort</code></td>
<td>진행 중인 rebase를 <strong>중단하고 원래 상태로</strong></td>
</tr>
</tbody></table>