<blockquote>
<p>현재 진행중인 실제 서비스 예정인 프로젝트에서 팀원들과 Devops에 대해 회의를 하다가
짧은 스프린트와 운영의 효율성 및 간편함을 위해 EC2가 아닌 ECS를 사용하기로 하였다.
ECS라는 걸 처음 들어보기도 했고, 찾아보니 알아야할 개념들이 꽤나 많길래 간단하게라도 정리해본다!</p>
</blockquote>
<h1 id="🤔ecselastic-container-serivce란">🤔ECS(Elastic Container Serivce)란?</h1>
<p><code>ECS(Elastic Container Serivce)</code> <strong>AWS에서 제공하는 컨테이너 오케스트레이션 서비스</strong>이다.
ECS를 사용하여 EC2 인스턴스의 관리형 클러스터나, AWS Fargate를 통한 서버리스 환경에서 애플리케이션을 손쉽게 실행할 수 있다.
ECS를 사용함으로써 얻는 이점은 다음과 같다.</p>
<ul>
<li><strong>다양한 AWS의 서비스 연계</strong>
  AWS에서 제공하고 있는 다양한 서비스들과 옵션들을 연계하여 더욱 효율적으로 컨테이너를 관리할 수 있다.</li>
<li><strong>제어 플레인 또는 노드 관리 불필요</strong>
  AWS Fargate를 통해 서버리스 환경을 이용할 수 있으며 제어 플레인 또는 노드를 관리, 인스턴스 패치 및 조정이 불필요하므로 운영에 소요되는 시간이 줄어든다.</li>
<li><strong>컴퓨팅 비용 절감</strong></li>
<li><strong>보안 및 규제 요규 사항 충족</strong></li>
</ul>
<hr />
<h2 id="✅ecs의-기본-용어-및-구성">✅ECS의 기본 용어 및 구성</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/e5ed8515-7179-4c1d-b44f-143957d66da3/image.png" /></p>
<h3 id="1-ecr">1. ECR</h3>
<ul>
<li>Docker 이미지를 저장하는 AWS 저장소(컨테이너 이미지 저장소)</li>
<li>내가 빌드한 이미지를 ECR에 올려두면 ECS에서 가져다가 실행 가능</li>
</ul>
<h3 id="2-fargate">2. Fargate</h3>
<ul>
<li>ECS의 실행 방식 중 하나</li>
<li>서버(EC2)를 직접 띄우지 않고 AWS가 실행 환경을 자동으로 제공한다.</li>
<li>완전 서버리스라 관리 부담이 줄어든다.</li>
</ul>
<h3 id="3-albapplication-load-balancer">3. ALB(Application Load Balancer)</h3>
<ul>
<li>가장 많이 사용되는 로드 밸런서</li>
<li>HTTP/HTTPS를 지원</li>
<li>Docker 컨테이너화된 애플리케이션과의 통합을 용이하게 한다.</li>
</ul>
<h3 id="4-vpcvirtual-private-cloud">4. VPC(Virtual Private Cloud)</h3>
<ul>
<li>ECS의 사용에 필요한 네트워크 리소스를 제공하는 서비스</li>
<li>AWS 안에서 내가 쓰는 가상의 네트워크라고 생각하면 쉽다</li>
<li>내가 쓰는 개인 전용 인테넛망(이란 느낌)</li>
</ul>
<h3 id="5-iam">5. IAM</h3>
<ul>
<li>클러스터나 서비스 등 다양한 작업의 권한을 설정하는 서비스</li>
</ul>
<h3 id="6-cloudwatch">6. CloudWatch</h3>
<ul>
<li>ECS 로그, 메트릭(리소스 사용량, 에러 등) 확인하는 모니터링 도구.</li>
</ul>
<hr />
<h2 id="✅ecs의-구성-요소">✅ECS의 구성 요소</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/661d6ab5-8b0e-4fb9-950c-3c2572eb662c/image.png" /></p>
<h3 id="1-task작업">1. Task(작업)</h3>
<ul>
<li>실행할 컨테이너(들)의 정의와 실제 실행 단위</li>
<li>Task ECS의 최소 단위는 task이며, 1개 이상의 컨테이너가 task에 구성된다.</li>
<li><code>Task ARN</code> : 태스크를 구분하는 고유 식별자</li>
</ul>
<h3 id="2-task-definition작업-정의">2. Task Definition(작업 정의)</h3>
<ul>
<li>어떤 이미지, CPU, 메모리, 포트, 환경변수 쓸건지 정의하는 설계도.</li>
<li>task의 구성 요소는 Task definition에 json 형식으로 작성되어 있다.</li>
<li>작성된 json 파일의 내용을 참고로 task가 구성된다.</li>
</ul>
<h3 id="3-container-instance">3. Container Instance</h3>
<ul>
<li>ECS를 통해 Task가 배포되는 EC2 인스턴스</li>
<li>Fargate에서는 적용되지 않는다.</li>
</ul>
<h3 id="4-service">4. Service</h3>
<ul>
<li>태스크를 몇 개 실행할지, 꺼지면 다시 띄울지, 로드 밸런서 연결할지 등 관리하는 상위 개념</li>
</ul>
<h3 id="5-cluster">5. Cluster</h3>
<ul>
<li>ECS 리소스들을 묶어두는 집합</li>
<li>컨테이너가 실행될 논리적인 그룹</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/abaeefb9-3222-4837-9e03-13ae33c1a0d0/image.png" /></p>
<blockquote>
<h3 id="📌단계별-작업-순서">📌단계별 작업 순서</h3>
</blockquote>
<ol>
<li>컨테이너의 이미지를 저장소(ECR)에 저장</li>
<li>task definition에서 사용할 이미지 및 리소스 정의</li>
<li>cluster 생성</li>
<li>service 생성 -&gt; task definition을 토대로 task 생성</li>
<li>ALB (로드밸런서) 연결 + 오토 스케일링</li>
</ol>
<hr />
<h2 id="✅그-외-알면-좋은-용어들">✅그 외 알면 좋은 용어들</h2>
<h3 id="1-subnet">1. Subnet</h3>
<ul>
<li>VPC를 나눈 블록</li>
<li><code>Subnet Id</code> : 서브넷 식별자<h3 id="2-security-group보안-그룹">2. Security Group(보안 그룹)</h3>
<ul>
<li>인바운드, 아웃바운드 트래픽 필터(방화벽)</li>
<li>포트/IP 허용<h3 id="3-eni">3. ENI</h3>
</li>
<li>태스크가 네트워크에 붙을때 할당되는 가상 네트워크 카드</li>
<li><code>ENI ID</code> : 네트워크 카드 식별자</li>
</ul>
</li>
</ul>
<h3 id="4-auto-scaling">4. Auto Scaling</h3>
<ul>
<li>트래픽 상황에 따라 태스크 자동 증감</li>
</ul>
<hr />
<pre><code class="language-pgsql">                  +------------------+
                  |   Developer      |
                  | (Docker build)   |
                  +--------+---------+
                           |
                           v
                  +------------------+
                  |       ECR        |
                  | (이미지 저장소)  |
                  +--------+---------+
                           |
                           v
               +-----------+------------+
               | Task Definition (설계도)|
               +-----------+------------+
                           |
                  +--------+---------+
                  |     ECS Service  |
                  | (태스크 관리)    |
                  +--------+---------+
                           |
          +----------------+----------------+
          |                                 |
      +---v---+                         +---v---+
      | Task  |                         | Task  |
      |컨테이너|                        |컨테이너|
      +---+---+                         +---+---+
          |                                 |
      ENI ID                             ENI ID
   (네트워크 카드)                   (네트워크 카드)
          |                                 |
  +----------------+                 +----------------+
  | Security Group |                 | Security Group |
  +----------------+                 +----------------+
          |                                 |
          +---------------+-----------------+
                          |
                  +------------------+
                  |       VPC        |
                  |   (내 전용망)    |
                  +------------------+
                          |
                  +------------------+
                  |   ALB (로드밸런서)|
                  +------------------+
                          |
                  +------------------+
                  |   End Users      |
                  +------------------+</code></pre>
