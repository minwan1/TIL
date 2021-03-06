## [DSLR](https://www.acmicpc.net/problem/9019)

### 문제
![](https://i.imgur.com/8IBTlPL.png)

일단 문제를 이해하면 아래의 요구사항부터 이해를 해야한다.

D: D 는 n을 두 배로 바꾼다. 결과 값이 9999 보다 큰 경우에는 10000 으로 나눈 나머지를 취한다. 그 결과 값(2n mod 10000)을 레지스터에 저장한다.
S: S 는 n에서 1 을 뺀 결과 n-1을 레지스터에 저장한다. n이 0 이라면 9999 가 대신 레지스터에 저장된다.
L: L 은 n의 각 자릿수를 왼편으로 회전시켜 그 결과를 레지스터에 저장한다. 이 연산이 끝나면 레지스터에 저장된 네 자릿수는 왼편부터 d2, d3, d4, d1이 된다.
R: R 은 n의 각 자릿수를 오른편으로 회전시켜 그 결과를 레지스터에 저장한다. 이 연산이 끝나면 레지스터에 저장된 네 자릿수는 왼편부터 d4, d1, d2, d3이 된다.

 문제는 대략 이렇다. 특정 숫자 예를들어 1234와 3412가 입력되었다고 했을때 위에 요구조건 DSLR을 만족하여 1234를 3412로 만들려고할 때 DSLR을 최소한의 갯수와 어떤순서대로 DSLR을 조합해야하는지를 구하는 문제이다. 이것은 직접 1234를 시작으로 BFS로 풀면 풀 수 있는 문제가 될 수 있을것이다. 소스는 같은 폴더에서 확인할 수 있다.
