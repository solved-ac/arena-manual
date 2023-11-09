# 데이터 및 채점

> 요약
>
> - 채점이 밀리지 않도록 데이터의 크기를 조절해야 합니다.
> - Polygon을 사용해 데이터를 작성해야 합니다. Polygon 패키지에 경고가 있어서는 안 됩니다.
> - Validator와 Checker는 C 또는 C++로 작성해야 하며, 검수 및 실수 탐지의 용이성을 제1 목표로 작성해야 합니다.
>   - Validator에서 등장하는 수 및 기호는 지문의 것을 그대로 따라야 합니다.
>   - Validator와 Checker에는 `#define`을 사용하지 않아야 합니다.
> - Checker는 맞는 소스를 틀리다고 판정할 가능성은 없는지 꼼꼼히 점검하면서 작성해야 합니다.
> - 데이터는 충분히 강력해야 합니다.

## 출제 분야별 주의사항

- 실수를 다루는 문제 (TODO: 오차 계산)
- 기하학 (TODO: 정수로 계산하기; 세 점이 한 직선 위에 있는지 validation 등)
- 이분 탐색, 매개 변수 탐색 (TODO: 범위에 주의하도록 함)
- 데이크스트라 (TODO)
- 플로우 (TODO: 틀린 팀노트)
- 문자열, 해싱 (TODO: 소수 저격하지 않도록 함)
- 무작위화 (TODO: 확률 계산)

## 목차

- [출제 분야별 주의사항](#출제-분야별-주의사항)
- [목차](#목차)
- [데이터의 크기](#데이터의-크기)
- [데이터의 개수](#데이터의-개수)
  - [참가 하한 Unrated에서](#참가-하한-unrated에서)
- [Polygon](#polygon)
  - [공통](#공통)
  - [테스트 작성](#테스트-작성)
  - [Generator 작성](#generator-작성)
  - [Validator 작성](#validator-작성)
  - [Checker 작성](#checker-작성)
- [데이터의 강력함](#데이터의-강력함)
- [점검 사항](#점검-사항)

## 데이터의 크기

데이터의 총 용량은 1GB를 넘지 않도록 합니다.

> **Note**
> 데이터의 총 용량은 BOJ에서 '채점 준비 중'이 표시되는 시간과 관련이 있습니다. BOJ에서 1GB의 데이터를 처리하는 데에는 경험적으로 ~5초가 소요되는 것으로 알려져 있습니다.

## 데이터의 개수

데이터의 수 곱하기 시간 제한(# &times; TL)은 아래 제한을 따릅니다.

| 예상 난이도   | # &times; TL |
| ------------- | ------------ |
| Bronze        | &le; 30초    |
| Silver        | &le; 90초    |
| Gold          | &le; 5분     |
| &ge; Platinum | &le; 15분    |

> **Note**
> \# &times; TL이 커질수록 제출 하나의 채점에 걸리는 시간이 늘어나며, 채점 큐가 밀릴 가능성이 있습니다.

### 참가 하한 Unrated에서

특히, 참가 제한이 Unrated부터인 대회에서, 가장 많은 참가자가 해결할 것으로 예상하는 문제에 대하여 아래의 추가 제한을 따릅니다.

| 예상 난이도 | # &times; TL |
| ----------- | ------------ |
| 가장 쉬움   | &le; 10초    |

## Polygon

Generator, validator, checker 및 interactor 준비에는 Codeforces의 [Polygon 플랫폼](https://polygon.codeforces.com/)과 `testlib.h`를 사용해야 합니다.

- 참고: [Codeforces: Briefly about testlib.h](https://codeforces.com/testlib)
- 참고: [Codeforces: Generators with testlib.h](https://codeforces.com/blog/entry/18291)
- 참고: [Codeforces: Validators with testlib.h](https://codeforces.com/blog/entry/18426)
- 참고: [Codeforces: Interactors with testlib.h](https://codeforces.com/blog/entry/18455)
- 참고: [Codeforces: Checkers with testlib.h](https://codeforces.com/blog/entry/18431)

### 공통

- **(경고 해결)** Polygon 패키지에는 no tags, no statement 등을 포함한 모든 경고가 없어야 합니다. 단, 다음 예외를 허용합니다.
  - Solution '`<solution>`' uses unusual int128-like type on the line `<n>`
- **(간결한 소스 코드)** Polygon에 작성하는 모든 코드는 검수에 용이하도록 매크로 등을 사용하지 말고 최대한 간결하게 작성합니다.
  - 매크로 및 템플릿의 사용은 다른 검수자가 이를 이해하는 데에 어려움을 주고, 검수 미비로 이어질 가능성이 높기 때문에 지양되어야 합니다.
  - 복잡한 로직은 최대한 많은 함수로 쪼갭니다.
  - 함수의 인자로 6개 이상의 변수를 받지 않으며, 필요한 경우 `struct` 등을 정의하여 사용합니다.
- **(외부 소스 코드)** 이미 구현체가 있는 알고리즘(플로우, FFT 등)의 소스를 사용하고자 하는 경우, 해당 내용과 메인 코드를 주석으로 구분하고 출처를 명시합니다.
  - 저자 명시의 의미도 있지만, 간혹 기반 구현체를 믿을 수 없는 경우도 있기 때문입니다.

이하의 내용이 경고를 해결하는 데에 도움이 될 수 있습니다.

### 테스트 작성

- 직접 생성한 데이터의 등록은 다음 경우를 제외하고 지양하며, 꼭 직접 생성한 데이터를 등록해야 한다면, description 란에 생성 방법 등을 명확히 설명합니다.
  - 예제에 사용하는 데이터
  - 데이터의 생성에 너무 오랜 시간이 걸려 Polygon에서 생성 시간 제한을 초과하는 데이터
- Validator에 정의한 변수에서, 최댓값을 만족하는 데이터와 최솟값을 만족하는 데이터가 각각 하나 이상 있어야 합니다. 현재 문제의 제한 상 그런 데이터를 작성할 수 없다면, 문제의 제한을 변경하거나 명확히 합니다.
  - **예)** 제한이 '정점의 개수 $N$과 간선의 개수 $M$에 대해 $1 \le N, M \le 10^5$'인 경우, $N=1$인 경우에 $M \le 0$이어야 하여 해당 데이터를 만들 수 없으므로, $N \ge 2$ 혹은 $M \ge 0$으로 수정.

### Generator 작성

- 참고: [testlib.h 비공식 문서: Generator](/appendix/testlib/README.md#generator)
- 참고: [Codeforces: Generators with testlib.h](https://codeforces.com/blog/entry/18291)

- Multitest generator는 사용하면 안 됩니다.
  - 하나의 generator가 명확한 argument 없이 여러 전략의 데이터를 생성하면 안 됩니다.
- Generator의 이름은 테스트 생성 전략을 설명하는 이름이어야 합니다. 의미 없는 이름을 정해서는 안 됩니다.
  - **나쁜 예)** `gen`, `gen_a`, `generator` 등
  - **좋은 예)** `genrand`, `genmax`, `gen_prime`, `gen_random_tree` 등
  - 길이는 길어도 상관없습니다.
- Generator argument는 testlib의 `opt`를 사용하여 받는 것을 권장합니다.
  - **예)** `genrand -n 100 -m 540 -b 2 -t 0`
    ```cpp
    int n = opt<int>("n");
    int m = opt<int>("m");
    int b = opt<int>("b");
    int t = opt<int>("t");
    ```
  - 참고: [testlib.h 비공식 문서: opt](/appendix/testlib/opt.md)

### Validator 작성

- 참고: [testlib.h 비공식 문서: Validator](/appendix/testlib/README.md#validator)
- 참고: [Codeforces: Validators with testlib.h](https://codeforces.com/blog/entry/18426)

Validator는 문제 패키지에서 가장 실수가 발생하기 쉽고, 실수가 발생했을 때의 영향이 큰 곳 중 하나로, **검수 및 실수 탐지의 용이성을 제1 목표로 두고** 작성해야 합니다.

- Validator는 C 혹은 C++로 작성해야 합니다.
- Validator 코드에서는 `#define`을 사용하면 안 됩니다.
  - 타입 재정의를 사용하려는 경우, `using` 또는 `typedef`를 사용합니다.
  - **안 좋은 예 1)** 아래와 같은 매크로 사용은 코드를 큰 폭으로 바꾸지만 모두에게 익숙하지 않고, 이로 인해 코드 해석의 시간 및 실수 가능성이 증가될 수 있으므로 사용하면 안 됩니다.
    ```cpp
    #define rep(a, b) for (int i = (a); i < (b); i++)
    #define all(v) (v).begin(), (v).end()
    ```
  - **안 좋은 예 2)** 아래와 같은 매크로는 이미 많은 사람이 학습한 기호를 재정의하여 혼선이 발생할 수 있으므로 사용하면 안 됩니다.
    ```cpp
    #define int long long
    ```
  - **좋은 예)**
    ```cpp
    using ll = long long;
    ```
- Validator 코드에 수(특히 제한)를 적는 경우, 지문에 적은 대로 적습니다. 1,000 단위 구분 기호를 적극적으로 사용합니다(UCPC 가이드라인을 따르는 지문의 경우 지문에 이미 1,000 단위 구분 기호가 있을 것입니다).
  - **예 1)** $1 \le N \le 100\\,000$ &rarr; `inf.readInt(1, 100'000, "N")`
  - **예 2)** $1 \le N \le 10^9$ &rarr; `inf.readInt(1, 1e9, "N")`
- `readInt`, `readWord` 등을 사용할 때 마지막 인자로 변수 이름을 지문에 적은 대로 적습니다.
  - **안 좋은 예 1)** $1 \le N \le 1\\,000$ &rarr; `inf.readInt(1, 1'000)`
  - **안 좋은 예 2)** $1 \le T \le 1\\,000$ &rarr; `inf.readInt(1, 1'000, "N")`: 번수명이 달라 검수 과정 중 헷갈릴 위험이 있습니다.
  - **안 좋은 예 3)** $1 \le N \le 1\\,000$ &rarr; `inf.readInt(1, 1'000, "n")`: 이후 문제 수정 과정 중에 casing이 다른 $n$ 등의 변수가 등장할 수 있으므로, 대소문자도 지문에 적은 대로 적는 것이 권장됩니다.
  - **좋은 예 1)** $1 \le N \le 1\\,000$ &rarr; `inf.readInt(1, 1'000, "N")`
  - **좋은 예 2)** $1 \le a_{ij} \le 10^6$ &rarr; `inf.readInt(1, 1e6, "a_ij")`
  - **좋은 예 3)** $1 \le a_{ij} \le 10^6$ &rarr; `inf.readInt(1, 1e6, format("a_(%d,%d)", i, j))`

### Checker 작성

- 참고: [testlib.h 비공식 문서: Checker](/appendix/testlib/README.md#checker)
- 참고: [Codeforces: Checkers with testlib.h](https://codeforces.com/blog/entry/18431)

Checker도 실수의 영향이 굉장히 큰 곳입니다. 검수 및 실수 탐지의 용이성을 고려하여 작성하되, 맞는 소스를 틀리다고 판정할 가능성은 없는지 꼼꼼히 점검하면서 작성하는 것이 무엇보다 중요합니다.

> **Important**
> 출력 형식이 중요한 문제가 아닐 경우 Checker에서 출력 형식을 검증해서는 안 됩니다. 본 매뉴얼은 일반적인 경우에서 `1 2 3`과 `1\n2\n3`을 같은 의미를 가지는 출력으로 보도록 하고 있습니다.
>
> BOJ는 기본적으로 줄 끝 공백을 허용하는 등 출력 형식에 엄격하지 않습니다. 이를 checker로 직접 구현하기는 다소 번거로우며, 보통 입출력 형식을 지키는 것 자체가 문제에서 물어보고자 하는 핵심 스킬인 경우는 드뭅니다. 따라서 아레나는 출력 형식 검증이 중요하지 않다고 보고 있습니다.

- Checker는 C 혹은 C++로 작성해야 합니다.
- Checker 코드에서는 `#define`을 사용하면 안 됩니다.
- BOJ의 스페셜 저지 기능을 사용하지 않으려는 경우, Polygon에서 checker는 `wcmp.cpp`로 정합니다.
  - 참고: [BOJ Help: 채점 결과#출력 형식이 잘못되었습니다](https://help.acmicpc.net/judge/info)
- Checker는 줄 끝 공백에 대하여 관대하여야 합니다.
  - **예 1)** 정답이 '`Impossible`'이라면, '`Impossible`'과 '`Impossible `' 모두 정답으로 처리해야 합니다.
  - **예 2)** 정답이 '`1 2 3`'이라면, '`1 2 3`'과 '`1 2 3 `' 모두 정답으로 처리해야 합니다.
- 출력 형식이 중요한 문제가 아닐 경우, checker 내에서 다음 함수를 사용하지 않습니다.
  - `readEoln()`
  - `readEof()`
  - `readSpace()`
  - 참고: [testlib.h 비공식 문서: InStream/Strict Mode](/appendix/testlib/instream.md#strict-mode)
- Checker는 참가자의 정답이 출제자의 정답보다 효율적인(정해가 틀리는) 사고가 발생할 가능성이 있음을 인지하고, [`_fail`](/appendix/testlib/tresult.md#_fail) 판정을 사용하여야 합니다.

## 데이터의 강력함

채점 데이터는 충분히 강력해야 하며, 의도하지 않은 풀이로 해결할 수 있는 경우를 최대한 방지하기 위해 여러 전략의 generator를 작성할 것을 권장합니다. 더 자세한 내용은 [풀이 및 제한 설정](/pre-contest/solution/README.md) 문서를 참고합니다.

다음 기계적으로 점검할 수 있는 사항들을 점검하고, 해당 데이터가 없다면 추가합니다.

- 기호의 입력 범위의 최소 또는 최댓값을 포함하는 입력 데이터가 있는가?
  - **예)** $1 \le N \le 100\,000$이라면 $N = 1$인 데이터와 $N = 100\,000$인 데이터가 하나 이상씩 있는지 점검합니다.
- 최선의 경우와 최악의 경우, 또는 그에 준하는 입력 데이터가 있는가?
  - **예)** 이분 탐색을 정해로 하는 문제에서 가능한 답 $X$의 범위가 $0 \le X \le 10\,000$일 때, $X = 0$인 데이터와 $X = 10\,000$인 데이터가 하나 이상씩 있는지 점검합니다.

데이터를 강력하게 만들기 위해 Polygon의 stress test를 활용할 수 있습니다.

## 점검 사항

다음 방법으로 문제가 훨씬 쉽게 해결될 여지가 있는지 점검합니다.

- [OEIS?](https://oeis.org/)
- [Inverse Symbolic Calculator?](https://wayback.cecm.sfu.ca/projects/ISC/ISCmain.html)
- [런타임 전의 전처리?](https://solved.ac/problems/tags/precomputation)
- [벌리캠프?](https://solved.ac/problems/tags/berlekamp_massey)
- [정규식?](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Regular_expressions)
- Python의 `in` 연산자? (한정된 경우의 KMP)
- Python의 `statistics` 모듈? (선형 회귀, 평균, 분산 등)
- Java의 `java.awt.geom`? (적은 수의 다각형의 불 연산)
- Ruby의 `Prime`? (밀러-라빈)

해당 방법으로 문제를 훨씬 쉽게 해결할 수 있는 것을 발견했다면, 해당 문제의 출제를 재고합니다.
