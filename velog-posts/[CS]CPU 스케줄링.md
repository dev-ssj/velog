<h1 id="💡cpu-스케줄링이란">💡CPU 스케줄링이란?</h1>
<blockquote>
<p>CPU 스케줄링은 언<strong>제 어떤 프로세스에 CPU를 할당할지 결정하는 작업</strong>을 의미한다. 이 알고리즘은 CPU 이용률은 높게, 주어진 시간에 많은 일을 하게, 준비 큐에 있는 프로세스는 적게, 응답시간은 짧게 설정하는 것을 목표로 한다.</p>
</blockquote>
<h2 id="✅프로세스-우선순위">✅프로세스 우선순위</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/ed595a31-10f3-4d43-bac5-2260485903e5/image.png" /></p>
<ul>
<li><strong>입출력 작업이 많은 프로세스의 우선순위는 CPU 작업이 많은 프로세스의 우선순위보다 높다</strong></li>
<li>입출력 작업이 많은 프로세스를 <code>입출력 집중 프로세스</code>, CPU 작업이 많은 프로세스를 <code>CPU 집중 프로세스</code>라한다.</li>
<li>CPU 집중 프로세스는 CPU를 많이 사용해야 하는 프로세스이고, 입출력 집중 프로세스는 그렇지 않은 프로세스인데 두 프로세스 모두 동일한 빈도로 CPU를 사용하는 것은 비합리적이다. 
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/9276a51e-8b52-43bd-b918-4bbd2b23bfba/image.png" /></li>
</ul>
<ul>
<li>그래서 <strong>운영체제는 프로세스의 중요도에 맞게 프로세스가 CPU를 이용할 수 있도록 우선순위를 부여</strong>한다. </li>
<li>각 프로세스의 <strong>PCB에 우선순위를 명시</strong>하고, 그 우선순위를 기준으로 먼저 처리할 프로세스를 결정하게 된다.</li>
</ul>
<hr />
<h2 id="✅스케줄링-큐">✅스케줄링 큐</h2>
<ul>
<li>운영체제가 <strong>프로세스들에게 공정하고 합리적으로 CPU 자원을 배분</strong>하는 것</li>
<li>CPU를 사용할 다음 프로세스를 찾기 위해 모든 프로세스의 PCB를 확인하는 것은 비효율적이기 때문에 각 자원을 사용하고 싶어하는 프로세스들을 줄을 세워서 관리한다. 이 줄을 <strong>스케줄링 큐</strong>라고 부른다.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/dcc5dd34-4533-4b55-a4ae-6e36a17093fe/image.png" /></p>
<h3 id="☑️준비-큐ready-queue">☑️준비 큐(Ready queue)</h3>
<ul>
<li>CPU를 이용하고 싶은 프로세스들이 서는 줄을 의미</li>
</ul>
<h3 id="☑️대기-큐waiting-queue">☑️대기 큐(waiting queue)</h3>
<ul>
<li>입출력장치를 이용하기 위해 대기 상태에 접어든 프로세스들이 서는 줄을 의미</li>
</ul>
<hr />
<h2 id="✅선점형-비선점형-스케줄링">✅선점형, 비선점형 스케줄링</h2>
<h3 id="☑️선점형-스케줄링">☑️선점형 스케줄링</h3>
<ul>
<li><strong>어떤 프로세스가 CPU를 할당받아 실행 중이더라도 운영체제가 CPU를 강제로 빼앗을 수 있는 스케줄링 방식</strong></li>
<li>인터럽트 처리 같이 현재 실행 중인 작업을 중단하고 다른 프로세스가 CPU를 사용할 수 있다.</li>
<li>한 프로세스의 자원 독점을 막고 골고루 자원 배분이 가능하지만, 그만큼 문맥 교환 과정에서 오버헤드가 발생할 수 있다.</li>
<li><code>RR(Round Robin)</code>, <code>SRT</code>, <code>다단계 큐</code>, <code>다단계 피드백 큐</code></li>
</ul>
<h3 id="☑️비선점형-스케줄링">☑️비선점형 스케줄링</h3>
<ul>
<li><strong>어떤 프로세스가 CPU를 점유하면 다른 프로세스가 이를 빼앗을 수 없는 스케줄링 방식</strong></li>
<li>프로세스가 종료되거나 자발적으로 대기 상태에 들어가기 전까지는 계속 CPU를 사용할 수 있는 방식</li>
<li>문맥 교환으로 발생하는 오버헤드가 적지만, 긴 작업이 CPU를 오랫동안 점유하면 다른 프로세스의 응답성이 크게 저하될 수 있다.</li>
<li><code>FCFS(First-Come, First-Served)</code>, <code>비선점형 SJF</code>, <code>HRN</code></li>
</ul>
<hr />
<h1 id="💡cpu-스케줄링-알고리즘">💡CPU 스케줄링 알고리즘</h1>
<h2 id="✅fcfsfirst-come-first-serverd">✅FCFS(First Come First serverd)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/bba24cca-761f-4094-8daf-4c0074fad216/image.png" /></p>
<ul>
<li><strong>준비 큐에 도착한 순서대로 CPU를 할당하는 비선점형 방식</strong>으로, 선입 선출 스케줄링이라고도 한다. </li>
<li>이 스케줄링 알고리즘은 처리 시간이 긴 프로세스가 CPU를 차지하면 다른 프로세스들은 하염없이 기다리느라 시스템의 효율성이 떨어지는데, 이를 <strong>호위효과(Convoy effect)</strong>라고 한다.</li>
</ul>
<br />

<h2 id="✅sjfshortest-job-first">✅SJF(Shortest Job First)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/93ea8a11-2966-4bc8-8973-10a1344baa36/image.png" /></p>
<ul>
<li>준비 큐에 있는 프로세스 중에서 <strong>실행 시간이 가장 짧은 작업부터 CPU를 할당하는 비선점형 방식</strong>으로, 최단 작업 우선 스케줄링이라고도 한다.</li>
<li>작은 작업을 먼저 실행하기 때문에 시스템의 효율성은 좋아지지만, 어떤 프로세스는 CPU 할당이 계속 연기될 수 있다 -&gt; <strong>기아 상태(Starvation)</strong></li>
</ul>
<h3 id="📌에이징aging기법">📌에이징(Aging)기법</h3>
<ul>
<li><strong>기아 상태를 해결하기 위한 대표적인 기법</strong></li>
<li>오랫동안 대기한 프로세스의 우선 순위를 나이처럼 점차 높이는 방식</li>
<li>프로세스가 시간이 지남에 따라 노화되어 결국 CPU를 할당받을 기회를 보장함으로써 Starvation을 해소</li>
</ul>
<br />

<h2 id="✅hrnhighest-response-ratio-next">✅HRN(Highest Response Ratio Next)</h2>
<ul>
<li>SJF 스케줄링에서 발생할 수 있는 <strong>기아 상태를 해결하기 위해 만들어진 비선점형 알고리즘</strong>으로, 최고 응답률 우선 스케줄링이라고도 한다.</li>
<li>SJF의 우선순위 판단 기준이 프로세스 실행 시간이었다면, HRN은 <strong>대기시간과 CPU의 사용시간(실행시간)을 고려</strong>하여 스케줄링 하는 방식이다.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/956eaa4d-d9d2-4c32-8949-b94befbfa528/image.png" /></li>
</ul>
<br />

<h2 id="✅rrround-robin">✅RR(Round Robin)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/0f13ad84-2aee-4926-aad4-9668a631e790/image.png" /></p>
<ul>
<li><strong>FCFS 스케줄링에 타임슬라이스라는 개념이 더해진 선점형 스케줄링 방식</strong></li>
<li><code>타임 슬라이스</code> : 각 프로세스가 CPU를 사용할 수 있는 정해진 시간을 의미</li>
<li>타임 슬라이스가 지나치게 크면 FCFS와 다를 바 없어 호위효과가 생길 여지가 있고, 타임 슬라이스가 지나치게 작으면 문맥 교환에 발생하는 비용이 커져 평균 대기시간이 더 크게 나올 수 있다.</li>
</ul>
<br />

<h2 id="✅srtshortest-remainig-time">✅SRT(Shortest Remainig Time)</h2>
<ul>
<li>** SJF 스케줄링과 라운드 로빈 스케줄링을 혼합한 방식**으로 최소 잔류 시간 우선 스케줄링이라고도 한다.</li>
<li><strong>SJF 스케줄링의 선점형 버전</strong></li>
<li>프로세스들은 정해진 타임슬라이스만큼 CPU를 사용하되, CPU를 사용할 다음 프로세스로는 남아있는 작업시간이 가장 적은 프로세스가 선택된다.</li>
<li>SJF 스케줄링과 마찬가지로 운영체제가 프로세스 종료 시간을 예측하기 어렵고 기아 상태가 일어나기 때문에 잘 사용하지 않는다.</li>
</ul>
<br /> 

<h2 id="✅다단계-큐-스케줄링multi-level-queue">✅다단계 큐 스케줄링(Multi-Level Queue)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/401d1318-f46e-4d3c-a420-b36db7c88d4d/image.png" /></p>
<ul>
<li><strong>우선순위에 따라 준비 큐를 여러개 사용하는 방식</strong></li>
<li>프로세스는 운영체제로부터 부여받은 우선순위에 따라 해당 우선순위의 큐에 삽입된다.</li>
<li>우선순위 큐는 라운드 로빈 방식으로 운영되므로 프로세스가 큐에 삽입되는 것만으로도 우선순위 큐에서의 PCB 우선순위가 결정된다.</li>
<li>우선순위는 <strong>고정 우선순위</strong>를 사용하며, 상단 우선순위 큐에 있는 모든 프로세스의 작업이 끝나야 다음 우선순위 큐의 작업이 시작된다.</li>
<li>우선순위가 높은 프로세스때문에 우선순위가 낮은 프로세스의 작업이 연기되는데, 이러한 문제를 해결하기 위해 다단계 피드백 큐 스케줄링이 등장한다.</li>
</ul>
<br />

<h2 id="✅다단계-피드백-큐-스케줄링multi-level-feedback-queue">✅다단계 피드백 큐 스케줄링(Multi-Level Feedback Queue)</h2>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/36e7b6f7-3c7a-4782-b549-4d71ffb7be65/image.png" /></p>
<ul>
<li><strong>다단계 큐 스케줄링 + 에이징 기법이 적용된 스케줄링 기법</strong></li>
<li>다단계 큐 스케줄링과 다른점은** 프로세스들이 큐 사이를 이동**할 수 있다는 점이다.</li>
<li>새로 준비 상태가 된 프로세스가 있다면 우선 순위가 가장 높은 우선순위 큐에 삽입되고, 일정 시간(타임 슬라이스)동안 실행된다.</li>
<li>만약 프로세스가 해당 큐에서 실행이 끝나지 않는다면 다음 우선순위 큐에 삽입되어 실행됨을 반복한다. -&gt; <strong>CPU를 오래 사용해야 하는 프로세스는 점차 우선순위가 낮아짐.</strong></li>
<li>또한, 어떤 프로세스의 CPU 이용 시간이 길면 낮은 우선순위 큐로 이동시키고, 어떤 프로세스가 낮은 우선순위 큐에서 너무 오래 기다린다면 높은 우선순위 큐로 이동시킬 수 있는 알고리즘이다.</li>
<li>구현이 복잡하지만, 가장 일반적인 CPU 스케줄링 알고리즘으로 알려져있다.</li>
</ul>