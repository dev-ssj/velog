<h1 id="silver-ii-dna-비밀번호---12891">[Silver II] DNA 비밀번호 - 12891</h1>
<p><a href="https://www.acmicpc.net/problem/12891">문제 링크</a> </p>
<h3 id="문제-설명">문제 설명</h3>
<p>평소에 문자열을 가지고 노는 것을 좋아하는 민호는 DNA 문자열을 알게 되었다. DNA 문자열은 모든 문자열에 등장하는 문자가 {‘A’, ‘C’, ‘G’, ‘T’} 인 문자열을 말한다. 예를 들어 “ACKA”는 DNA 문자열이 아니지만 “ACCA”는 DNA 문자열이다. 이런 신비한 문자열에 완전히 매료된 민호는 임의의 DNA 문자열을 만들고 만들어진 DNA 문자열의 부분문자열을 비밀번호로 사용하기로 마음먹었다.</p>

<p>하지만 민호는 이러한 방법에는 큰 문제가 있다는 것을 발견했다. 임의의 DNA 문자열의 부분문자열을 뽑았을 때 “AAAA”와 같이 보안에 취약한 비밀번호가 만들어 질 수 있기 때문이다. 그래서 민호는 부분문자열에서 등장하는 문자의 개수가 특정 개수 이상이여야 비밀번호로 사용할 수 있다는 규칙을 만들었다.</p>

<p>임의의 DNA문자열이 “AAACCTGCCAA” 이고 민호가 뽑을 부분문자열의 길이를 4라고 하자. 그리고 부분문자열에 ‘A’ 는 1개 이상, ‘C’는 1개 이상, ‘G’는 1개 이상, ‘T’는 0개 이상이 등장해야 비밀번호로 사용할 수 있다고 하자. 이때 “ACCT” 는 ‘G’ 가 1 개 이상 등장해야 한다는 조건을 만족하지 못해 비밀번호로 사용하지 못한다. 하지만 “GCCA” 은 모든 조건을 만족하기 때문에 비밀번호로 사용할 수 있다.</p>

<p>민호가 만든 임의의 DNA 문자열과 비밀번호로 사용할 부분분자열의 길이, 그리고 {‘A’, ‘C’, ‘G’, ‘T’} 가 각각 몇번 이상 등장해야 비밀번호로 사용할 수 있는지 순서대로 주어졌을 때 민호가 만들 수 있는 비밀번호의 종류의 수를 구하는 프로그램을 작성하자. 단 부분문자열이 등장하는 위치가 다르다면 부분문자열이 같다고 하더라도 다른 문자열로 취급한다.</p>

<h3 id="입력">입력</h3>
 <p>첫 번째 줄에 민호가 임의로 만든 DNA 문자열 길이 |S|와 비밀번호로 사용할 부분문자열의 길이 |P| 가 주어진다. (1 ≤ |P| ≤ |S| ≤ 1,000,000)</p>

<p>두번 째 줄에는 민호가 임의로 만든 DNA 문자열이 주어진다.</p>

<p>세번 째 줄에는 부분문자열에 포함되어야 할 {‘A’, ‘C’, ‘G’, ‘T’} 의 최소 개수가 공백을 구분으로 주어진다. 각각의 수는 |S| 보다 작거나 같은 음이 아닌 정수이며 총 합은 |S| 보다 작거나 같음이 보장된다.</p>

<h3 id="출력">출력</h3>
 <p>첫 번째 줄에 민호가 만들 수 있는 비밀번호의 종류의 수를 출력해라.</p>


<h3 id="예제">예제</h3>
<p> <img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/8953f8ff-dacf-4c4f-bb03-5c28e5e84eb9/image.png" /></p>
<hr />
<h1 id="✅나의-문제-풀이">✅나의 문제 풀이</h1>
<pre><code class="language-java">public class Main {
    static int checkArr[];
    static int myArr[];
    static int checkSecret;
    public static void main(String[] args) throws IOException {
        BufferedReader bf = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(bf.readLine());

        //DNA 문자열의 길이
        int S = Integer.parseInt(st.nextToken());
        //부분 문자열의 길이
        int P = Integer.parseInt(st.nextToken());
        //정답 값 저장받을 변수
        int Result = 0;
        //비밀번호 체크 배열(A가 몇개 필요한지, G가 몇개 필요한지 등 기준 저장 하는 배열. 예시에서는 2 0 1 1)
        checkArr = new int[4];
        //현재 상태 저장할 배열(현재 A가 몇개있고, G가 몇개 있고.. myArr의 값이 checkArr와 같으면 조건 충족되는것 -&gt;checkSecret++)
        myArr = new int[4];
        //4개중 현재 몇개가 비밀번호 요건에 충족되는지 카운트 하는 변수 -&gt; checkSecret이 4면 result ++
        checkSecret = 0;
        //문자열을 입력받을 배열
        char[] A = bf.readLine().toCharArray();

        st = new StringTokenizer(bf.readLine());
        //각각의 dna가 몇개 필요한지 입력 받는 for문(2 0 1 1)
        for (int i = 0; i &lt; 4; i++) {
            checkArr[i] = Integer.parseInt(st.nextToken());
            //checkArr[i]가 0이면 해당 DNA는 없어도됨 -&gt; 이미 조건이 충족되었으니 checkSecret++
            if (checkArr[i] == 0)
                checkSecret++;
        }

        //초기(첫번째) 부분 문자열 처리 부분
        for (int i = 0; i &lt; P; i++) {
            Add(A[i]);
        }

        //checkSecret이 4 -&gt; 조건 모두 충족하여 유효한 비밀번호임
        if (checkSecret == 4)
            Result++;

        // 슬라이딩 윈도우 처리 부분
        int left = 0;   //왼쪽 값
        int right = P;  //오른쪽 값
        while(right &lt; S){   //배열 인덱스는 0부터 시작이므로 right &lt; S까지
            Add(A[right++]);  
            Remove(A[left++]);  
            if (checkSecret == 4)
                Result++;
        }
        System.out.println(Result);
        bf.close();
    }

    //새로 들어운 문자를 처리해주는 메서드
    //각각의 문자열 중 같은게 있으면 myArr[]에 1을 더해주고, myArr[]과 checkArr[]가 같다면 checkSecret++
    private static void Add(char c) {
        switch (c) {
            case 'A':
                myArr[0]++;
                if (myArr[0] == checkArr[0])
                    checkSecret++;
                break;
            case 'C':
                myArr[1]++;
                if (myArr[1] == checkArr[1])
                    checkSecret++;
                break;
            case 'G':
                myArr[2]++;
                if (myArr[2] == checkArr[2])
                    checkSecret++;
                break;
            case 'T':
                myArr[3]++;
                if (myArr[3] == checkArr[3])
                    checkSecret++;
                break;
        }
    }

    //제거되는  문자를 처리해주는 메서드
    //각각의 문자열 중 같은게 있으면 myArr[]에 1을 빼주고, myArr[]과 checkArr[]가 같다면 checkSecret--
    private static void Remove(char c) {
        switch (c) {
            case 'A':
                if (myArr[0] == checkArr[0])
                    checkSecret--;
                myArr[0]--;
                break;
            case 'C':
                if (myArr[1] == checkArr[1])
                    checkSecret--;
                myArr[1]--;
                break;
            case 'G':
                if (myArr[2] == checkArr[2])
                    checkSecret--;
                myArr[2]--;
                break;
            case 'T':
                if (myArr[3] == checkArr[3])
                    checkSecret--;
                myArr[3]--;
                break;
        }
    }
}</code></pre>
<ul>
<li>이 문제는 크기를 유지한 상태로 한칸씩 이동하면서 조건에 맞는지 탐색하는 슬라이딩 윈도우를 이용하는 문제이다.</li>
<li>주어진 DNA 문자열을 대조하면서 조건에 맞는지 탐색해야한다.</li>
</ul>
<hr />
<h1 id="✅문제-핵심-짚어보기">✅문제 핵심 짚어보기</h1>
<h2 id="필요한-변수-설정">필요한 변수 설정</h2>
<p>이 문제는 변수가 많아서 헷갈리기 쉬운데, 변수를 먼저 짚고 넘어가보도록하자.
<img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/84af6598-a394-42ff-bc63-e4757106768f/image.png" />
첫째줄에는 입력받을 전체 DNA 문자열의 길이와 부분 문자열의 길이를 입력받고, 두번째 줄에는 DNA 문자열을 입력받는다. 마지막으로 세번쨰 문자열은 부분 문자열에 포함되어야 할 A,C,G,T의 최소 개수를 입력받는다.</p>
<ul>
<li>첫째줄 : 입력받을 전체 DNA 문자열의 길이(<strong>S</strong>), 부분 문자열의 길이(<strong>P</strong>)</li>
<li>둘째줄 : DNA 문자열(<strong>char[] A</strong>)</li>
<li>셋째줄 : 부분 문자열에 포함되어야 할 A,C,G,T의 최소 개수(<strong>int[] checkArr</strong>)</li>
<li>결과 값을 입력받을 변수(<strong>result</strong>)</li>
</ul>
<p>예제에 나온 걸로 파악했을때 변수는 이렇게 5가지이다.
하지만 문제풀이를 할때 변수가 더 필요한데 먼저, 현재 A가 몇개있고, G가 몇개 있고 등 현재 상태를 저장할 배열 <strong>int[] myArr</strong>가 필요하다.
또, myArr의 값과 checkArr의 값이 같으면 해당 문자는 조건이 충족되는 것이므로 그걸 체크할 변수 <strong>checkSecret</strong>도 필요하다. checkSecret이 4면 해당 비밀번호는 유효한 비밀번호 이므로 result를 1증가시켜주면 된다.</p>
<h2 id="☑️흐름-파악-하기">☑️흐름 파악 하기</h2>
<h4 id="1-우선-입력받은-전체-dna-문자열을-a배열에-저장하고-비밀번호-체크-배열인-checkarr에-각-문자의-최소-개수를-저장한다">1. 우선 입력받은 전체 DNA 문자열을 A배열에 저장하고, 비밀번호 체크 배열인 checkArr에 각 문자의 최소 개수를 저장한다.</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/ac25971d-9765-43a5-991e-91cff93b8be2/image.png" /></p>
<h4 id="2-슬라이딩-윈도우에-포함된-문자열로-현재-상태-배열을-만들고-현재-상태-배열과-비밀-번호-체크-배열을-비교하여-유효한-비밀번호-인지-판단한다">2. 슬라이딩 윈도우에 포함된 문자열로 현재 상태 배열을 만들고, 현재 상태 배열과 비밀 번호 체크 배열을 비교하여 유효한 비밀번호 인지 판단한다.</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/2c91ecc0-2961-4c39-a29d-89fd375f864e/image.png" /></p>
<ul>
<li>p크기 만큼 문자열을 잘라서 비교해봐야하므로 p크기만큼의 부분 문자열을 A 배열에서 가져온다.</li>
<li>그리고 비밀번호 체크 배열(checkArr)와 현재상태 배열(myArr)를 비교하여 조건에 부합하면 각 문자마다 checkSecret에 1을 더해준다.</li>
<li>예제에서는 A의 조건은 2개이상이지만 현재 상태는 1개 이므로 조건에 부합하지 않는다.</li>
</ul>
<h4 id="3-윈도우를-한칸씩-이동하며-현재-상태-배열을-업데이트한다-현재-상태-배열을-업데이트할-때는-빠지는-문자열왼쪽에서-한칸-추가되는-신규-문자열오른쪽에서-한칸만-확인하여-업데이트-하는-방식으로-진행한다">3. 윈도우를 한칸씩 이동하며 현재 상태 배열을 업데이트한다. 현재 상태 배열을 업데이트할 때는 빠지는 문자열(왼쪽에서 한칸), 추가되는 신규 문자열(오른쪽에서 한칸)만 확인하여 업데이트 하는 방식으로 진행한다.</h4>
<p><img alt="" src="https://velog.velcdn.com/images/dev_ssj/post/be3113f4-6f97-4b65-ad0a-1bebc4cf8699/image.png" /></p>
<ul>
<li>윈도우를 한칸씩 이동하게 되면 맨 왼쪽의 C가 빠지고, 맨 오른쪽의 G가 추가된다.</li>
<li>마찬가지로 비밀번호 체크 배열(checkArr)와 현재상태 배열(myArr)를 비교하여 조건에 부합하면 각 문자마다 checkSecret에 1을 더해준다.</li>
<li>위 예제도 유효한 비밀번호가 아니다.</li>
</ul>
<h4 id="4-a-배열의-끝에-도달할-때-까지-한칸씩-이동한다">4. A 배열의 끝에 도달할 때 까지 한칸씩 이동한다.</h4>
<hr />
<h2 id="☑️코드-순서대로-뜯어보기">☑️코드 순서대로 뜯어보기</h2>
<p>입력받을 값을 모두 입력받았다는 전제하에 코드를 뜯어보자.(A,C,G,T의 최소 개수 입력 제외)</p>
<h4 id="1-문자-갯수acgt-총-4만큼-for문을-돌려-checkarr에-저장">1. 문자 갯수(A,C,G,T 총 4)만큼 for문을 돌려 checkArr에 저장</h4>
<pre><code class="language-java">//각각의 dna가 몇개 필요한지 입력 받는 for문(2 0 1 1)
for (int i = 0; i &lt; 4; i++) {
    checkArr[i] = Integer.parseInt(st.nextToken());
    //checkArr[i]가 0이면 해당 DNA는 없어도됨 -&gt; 이미 조건이 충족되었으니 checkSecret++
    if (checkArr[i] == 0)
        checkSecret++;
}</code></pre>
<ul>
<li>마지막 줄에 입력받는 A,C,G,T의 최소 개수를 for문을 돌려서 <code>checkArr</code>에 저장한다.</li>
<li>순서대로 A,C,G,T이며, 첫번째 예시를 예로 들면 (2 0 1 1)</li>
<li>checkArr[0] = 2, checkArr[1] = 0, checkArr[2] = 1, checkArr[1] 이 저장된다.</li>
<li>만약 해당 값이 0이라면(checkArr[1] = 0) 해당 DNA는 문자열에 없어도 된다. 즉, 이미 이 문자는 조건이 충족된것이므로 checkSecret을 증가시켜준다.</li>
</ul>
<h4 id="2-초기첫번째부분-문자열을-처리한다">2. 초기(첫번째)부분 문자열을 처리한다.</h4>
<pre><code class="language-java">//초기(첫번째) 부분 문자열 처리 부분
for (int i = 0; i &lt; P; i++) {
    Add(A[i]);
}

//checkSecret이 4 -&gt; 조건 모두 충족하여 유효한 비밀번호임
if (checkSecret == 4) Result++;

//새로 들어운 문자를 처리해주는 메서드
//각각의 문자열 중 같은게 있으면 myArr[]에 1을 더해주고,
//myArr[]과 checkArr[]가 같다면 checkSecret++
private static void Add(char c) {
    switch (c) {
        case 'A':
            myArr[0]++;
            if (myArr[0] == checkArr[0])
                checkSecret++;
            break;
        case 'C':
            myArr[1]++;
            if (myArr[1] == checkArr[1])
                checkSecret++;
            break;
        case 'G':
            myArr[2]++;
            if (myArr[2] == checkArr[2])
                checkSecret++;
            break;
        case 'T':
            myArr[3]++;
            if (myArr[3] == checkArr[3])
                checkSecret++;
            break;
        }
    }</code></pre>
<ul>
<li>첫번째 문자열은 슬라이딩하지 않고 바로 각 문자만 비밀번호 체크 배열(checkArr)와 비교해준다.</li>
<li>case 문을 이용해서 각각 어떤 문자인지 분기를 나누고, 현재상태 배열(myArr)을 업데이트 해준 후 현재상태 배열(myArr)와  비밀번호 체크 배열(checkArr)의 값이 같으면 checkSecret에 1을 더해준다.</li>
<li>그 후, checkSecret이 4가 되면 해당 비밀번호는 유효한 비밀번호이므로 결과값을 저장하는 result에 1을 더해준다.</li>
</ul>
<h4 id="3-슬라이딩-윈도우를-이용하여-처리한다">3. 슬라이딩 윈도우를 이용하여 처리한다.</h4>
<pre><code class="language-java"> // 슬라이딩 윈도우 처리 부분
int left = 0;   //왼쪽 값
int right = P;  //오른쪽 값
while(right &lt; S){   //배열 인덱스는 0부터 시작이므로 right &lt; S까지
    Add(A[right++]);  
    Remove(A[left++]);  
    if (checkSecret == 4)
        Result++;
    }
    System.out.println(Result);
    bf.close();
}

//제거되는  문자를 처리해주는 메서드
//각각의 문자열 중 같은게 있으면 myArr[]에 1을 빼주고
//myArr[]과 checkArr[]가 같다면 checkSecret--
private static void Remove(char c) { 
    switch (c) {
        case 'A':
             if (myArr[0] == checkArr[0])
                 checkSecret--;
            myArr[0]--;
            break;
        case 'C':
            if (myArr[1] == checkArr[1])
                checkSecret--;
            myArr[1]--;
            break;
        case 'G':
             if (myArr[2] == checkArr[2])
                 checkSecret--;
            myArr[2]--;
             break;
        case 'T':
            if (myArr[3] == checkArr[3])
                checkSecret--;
            myArr[3]--;
            break;
    }
    }
}</code></pre>
<ul>
<li>초기부분이 지나면 한칸씩 이동하여 슬라이딩하고, 문자를 체크해야한다.</li>
<li>슬라이딩 부분만 빼면 초기 문자 비교는 똑같다.</li>
<li>우선 슬라이딩 시작과 끝을 알리는 left, right 변수를 선언하고, right변수가 전체 문자열의 길이(S)보다 작을동안 while문을 돌려준다. -&gt; right &lt; S 로 범위를 설정한 이유는 배열 인덱스는 0부터 시작이므로!</li>
<li>그 후, 한칸 옮겨간 right는 포함하고, 한칸 빠진 left는 빼준다.</li>
<li>Add메서드를 이용해서 A[right]를 추가해주고, 후위연산으로 right를 증가시킨다.</li>
<li>Remove메서드를 이용해서 A[left]는 제거하고, 후위연산으로 left를 감소시킨다.</li>
</ul>
<h4 id="4-결과값을-출력한다">4. 결과값을 출력한다.</h4>
<ul>
<li>result값을 출력한다.</li>
</ul>