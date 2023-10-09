# testlib.h 비공식 문서

testlib.h version `0.9.40-SNAPSHOT`

## 목차

- [정의](#정의)
- [Generator](#generator): 출제자 입력 생성기.
- [Validator](#validator): 출제자 입력 검증기.
- [Checker](#checker): 참가자 답안 검증기.
- [Interactor](#interactor): 참가자 답안과 테스트의 상호작용.
- 메서드
  - [전역 유틸리티 메서드](./utils.md)
  - [InStream](./instream.md): 테스트와 답안의 입력 및 검증, 및 `inf`, `ouf`, `ans`.
  - [opt](./opt.md): generator 옵션 파싱.
  - [random_t](./random_t.md): 무작위 값 생성 및 전역 변수 `rnd`.
  - [pattern](./pattern.md): 문자열 매칭 및 표현식.
- 타입
  - [TResult](./tresult.md): 채점 결과.
- [참고](#참고)

## 정의

- $x_\mathbb{R}$: 참값 $x$를 실수로 표현한 값.
- $x_\mathbb{F}$: 참값 $x$를 가장 가까운 부동소수점 값으로 표현한 값.
- $\text{NaN}$: `__testlib_isNaN`이 `true`인 값.
- $\pm \infty$: `__testlib_isInfinite`가 `true`인 값, i. e. $x > 10^{300}$ 혹은 $x < -10^{300}$인 값.
- 기대하는 값 $x$와 실제 값 $y$의 절대 오차: $\left|x - y\right|$.
- 기대하는 값 $x$와 실제 값 $y$의 상대 오차: $\left|\frac{x - y}{x}\right|$.
- 공백 문자: `\t` = 9, `\n` = 10, `\r` = 13, ` ` = 32 중 하나.
- 개행 문자: `\n` = 10 혹은 `\r` = 13 중 하나.
- '정규 표현식': `pattern`이 처리하는 표현식. 일반적인 정규 표현식과는 조금 다릅니다. (침고: [pattern: 일반적인 정규식과 다른 점](./pattern.md#일반적인-정규식과-다른-점))

## Generator

Generator는 출제자가 입력을 생성할 때 사용하는 프로그램입니다.

Generator는 `stdout`으로 테스트 케이스를 생성해 출력합니다. 이론적으로는 testlib.h를 사용할 필요는 없지만, 랜덤 데이터 생성 등에 있어서 실행 시마다 같은 결과를 보장하는 [`random_t`](random_t.md) 및 테스트 데이터 생성에 도움이 되는 여러 유틸리티를 사용하기 위해 testlib.h를 사용하는 것이 좋습니다. testlib.h를 사용하려면, `main`의 첫 줄에 `registerGen(argc, argv, 1)`을 추가해 주어야 합니다.

보통 generator는 커맨드라인 변수로 지문에서의 제한($N$, $M$ 등)을 입력받아 그 크기의 데이터를 생성합니다. 일반적인 generator 실행 방법의 예는 다음과 같습니다.

```
generator -N=100000 -M=100000 -tree
```

이런 방법을 사용하면 generator 자체에서 데이터의 범위 등을 검증할 필요는 없으며, generator가 범위 밖의 데이터를 생성한다면 커맨드라인 변수의 값을 바꿔 주는 것으로 충분합니다. 데이터의 범위 검증 등은 validator가 담당합니다.

다만, **입력 데이터의 형식은 엄밀하게 지켜야 합니다**. 예를 들어, 언어의 한계로 인하여 토큰 단위 입력을 받는 것이 권장되지 않는 Python 사용자들은 공백으로 구분된 정수열을 다음과 같이 입력받는 것이 일반적입니다.

```python
a = list(map(int, input().split(' ')))
```

여기서, 아래와 같이 생성된 데이터를 입력받는다고 생각해 봅시다.

```cpp
for (int i = 0; i < n; i++) {
    cout << a[i] << ' ';
}
cout << '\n';
```

이렇게 생성한 데이터의 경우 줄 끝에 공백이 있습니다. 이때 `input().split()` 시 맨 마지막 원소는 `''`가 되며, `int('')`를 실행하면 런타임 에러가 발생합니다. 이런 경우를 막기 위해 generator에서는 다음과 같이 입력을 생성해야 합니다.

```cpp
for (int i = 0; i < n; i++) {
    if (i != 0) cout << ' ';
    cout << a[i];
}
cout << '\n';
```

상기 입력은 유틸 함수 [`println`](utils.md#println)을 사용하여 간단히 작성할 수 있습니다.

```cpp
println(a);
```

참가자 입장에서 문제를 해결할 때의 일반적인 출력 방식과는 차이가 있음에 주의하십시오.

입력 형식은 이후 validator에서 한 번 더 검증하게 됩니다.

- 참고: [opt](opt.md), [InStream](instream.md), [random_t](random_t.md)

## Validator

> BOJ Stack에서 validator는 [데이터 검증]에 대응됩니다.

Validator는 출제자의 입력이 문제의 제한과 입력 형식을 만족하는지 검증하는 프로그램입니다. Validator는 generator로 생성한 데이터를 검증해야 할 수도 있고, 여러 가지 이유로 출제자가 generator를 거치지 않고 직접 생성한 데이터를 검증해야 할 수도 있습니다. 출제자의 입력은 [`inf`](instream.md#inf-ouf-ans)에 들어오며, `main`의 첫 줄에 `registerValidation(argc, argv)`를 추가해 주어야 합니다.

[InStream](instream.md)의 여러 메서드를 이용해 입력의 범위를 검증합니다. 예를 들어, [`inf.readInt(1, 100'000, "N")`](instream.md#instreamreadint)는 입력이 $1 \le N \le 100\,000$을 만족하는지 검증합니다. 이때, `inf.readInt()`는 입력이 정수를 읽어들이는 것을 의미합니다. [`inf.readLong()`](instream.md#instreamreadlong), [`inf.readDouble()`](instream.md#instreamreaddouble) 등도 있습니다.

예를 들어, [BOJ 22988](https://www.acmicpc.net/problem/22988) 〈재활용 캠페인〉은 첫 줄에 정수 $1 \le N \le 100\,000$ 과 $1 \le X \le 10^{18}$ 을 입력받고, 두 번째 줄에 $N$개의 정수 $0 \le C_i \le X$ 를 입력받는 문제입니다. 이 경우 validator는 다음과 같이 작성할 수 있습니다.

```cpp
int main(int argc, char* argv[]) {
    registerValidation(argc, argv);

    // Line #1
    int N = inf.readInt(1, 100'000, "N");
    inf.readSpace();
    long long X = inf.readLong(1, (long long) 1e18l, "X");
    inf.readEoln();

    // Line #2
    inf.readLongs(N, (long long) 0, X, "C");
    inf.readEoln();

    inf.readEof();
}
```

- `1e18l` 대신 `1e18`로 적어도 문제는 없으나, `1e18` 정도의 크기의 값은 `double`이 정확히 표현할 수 없음에 주의합니다($10^{18}>10^{16}>2^{53}$). $10^{15}$ 이상의 값에서는 `l` 접미사를 붙여 `long double` 리터럴로 취급하는 것을 권장합니다.

Validator는 문제의 제한을 만족함을 검증하기 위하여 복잡한 로직을 포함할 수도 있습니다. 예를 들어, [BOJ 28436](https://www.acmicpc.net/problem/28436) 〈돌 옮기기〉는 `W`, `B`, `.`으로만 이루어진 문자열을 입력받고, 이 문자열에 `W` 및 `B`가 적어도 하나 이상 존재함을 검증해야 합니다. 이때의 validator는 다음과 같이 작성할 수 있습니다.

```cpp
int main(int argc, char *argv[]) {
    registerValidation(argc, argv);

    int T = inf.readInt(1, 10'000, "T"), totN = 0;
    inf.readEoln();

    while (T--) {
        string s = inf.readToken("[WB.]{2,500000}", "s");
        inf.readEoln();

        ensuref(
                count(s.begin(), s.end(), 'W') >= 1,
                "there should be at least one W, but got %s instead",
                compress(s).c_str()
        );
        ensuref(
                count(s.begin(), s.end(), 'B') >= 1,
                "there should be at least one B, but got %s instead",
                compress(s).c_str()
        );
        totN += s.size();
        ensuref(
                totN <= 500'000,
                "the sum of N over all cases should not exceed 500,000"
        );
    }
    inf.readEof();
}
```

[`readEoln`](instream.md#instreamreadeoln), [`readSpace`](instream.md#instreamreadspace), [`readEof`](instream.md#instreamreadeof) 등으로 입력 형식도 엄밀히 검증하고 있음에 유의합니다. [`ensuref`](utils.md#ensuref)는 입력이 조건을 만족하지 않을 경우, `format`으로 포매팅된 메시지를 출력하고 프로그램을 종료합니다. `ensuref`는 `assert`와 유사하게 동작합니다.

- 참고: [InStream](instream.md), [pattern](pattern.md), [ensuref](utils.md#ensuref)

## Checker

> BOJ Stack에서 checker는 [스페셜 저지]에 대응됩니다.

Checker는 참가자의 답안이 정답인지 검증하는 프로그램입니다. Checker는 `inf`, `ans`와 `ouf`를 이용해 출제자가 준비한 테스트 케이스와 출제자의 답안, 그리고 참가자의 답안을 읽어들입니다. `main`의 첫 줄에 `registerTestlibCmd(argc, argv)`를 추가해 주어야 합니다.

참가자의 입력은 validator에서 출제자의 입력을 검증하던 것보다는 덜 엄격하게 검증해도 괜찮습니다. 오히려 덜 업격하게 검증하는 것이 권장됩니다. 예를 들어, 5개의 정수 1, 2, 3, 4, 5를 공백으로 구분하여 출력할 때 참가자가 `1 2 3 4 5 `와 같이 출력했다면, 맨 뒤의 띄어쓰기가 있더라도 정답으로 처리합니다.

일반적으로 checker에서는 [`readEoln`](instream.md#instreamreadeoln), [`readSpace`](instream.md#instreamreadspace), [`readEof`](instream.md#instreamreadeof) 등을 사용하지 않고, 토큰 단위로만 읽습니다.

채점이 완료된 후 토큰이 남아 있을 경우 [`_pe`](tresult.md#_pe)로 자동 종료되므로, 출력 형식에 정의한 대로 참가자의 출력의 모든 토큰을 읽어야 하며, 일부 토큰만 읽고 도중 종료하면 안 됩니다. 같은 이유로, 참가자의 입력에 토큰이 남아 있는지를 검증할 필요는 없습니다. (참고: [`_dirt`](tresult.md#))

예를 들어, [BOJ 1008](https://www.acmicpc.net/problem/1008) 〈A/B〉의 checker는 [`doubleCompare`](utils.md#doublecompare)를 사용하여 아래와 같이 작성할 수 있습니다.

```cpp
int main(int argc, char *argv[]) {
    registerTestlibCmd(argc, argv);

    // Participant answer
    double pans = ouf.readDouble();

    // Jury answer
    double jans = ans.readDouble();

    if (doubleCompare(jans, pans, 1e-9)) {
        quitf(_ok, "OK %lf", pans);
    } else {
        quitf(_wa, "Expected %lf, but found %lf", jans, pans);
    }
}
```

- 참고: [InStream](instream.md), [Strict mode](instream.md#strict-mode), [TResult](tresult.md)
- 참고: [doubleCompare](utils.md#doublecompare)

## Interactor

> BOJ Stack에 interactor를 올리기 위해서는 스타트링크에 별도로 문의해야 합니다.

Interactor는 인터랙티브 문제에서 참가자의 프로그램과 상호작용을 하는 프로그램입니다. Interactor는 우선 `inf`를 통해 출제자의 테스트 케이스를 읽어들인 후, `ouf`를 통해 참가자의 프로그램의 출력을 읽고 `std::cout`을 통해 다시 입력을 전달하는 상호작용을 반복합니다. `main`의 첫 줄에 `registerInteraction(argc, argv)`를 추가해 주어야 합니다.

`ouf`를 읽을 때에는 checker와 동일하게 토큰 단위로 읽는 것이 권장됩니다. `cout`으로 한 줄을 출력한 후에는 반드시 `endl`이나 `cout.flush()` 등을 이용하여 출력 스트림을 flush하여야 합니다.

### Checker를 같이 사용하는 경우

Interactor는 추가적인 검증이 필요한 정보를 `tout`으로 출력할 수 있습니다. `tout`으로 출력한 파일은 checker에서 `ouf`로 읽게 됩니다. 출제자의 답안에 의해 작성된 `tout`은 interactor와 checker에서 `ans`를 통해 읽을 수 있습니다. 참가자의 코드는 interactor와 checker에서 모두 `_ac`를 받아야 통과합니다.

`tout`은 `cout`과 같은 방식으로 동작하며, `tout << n << endl;`과 같이 쓸 수 있습니다.

### Checker를 사용하지 않는 경우

인터랙티브 문제를 만들 때, 별도의 checker 없이 모든 채점을 interactor 내에서 하게 되는 경우가 많습니다. 이런 경우에도 Polygon에서는 checker가 없으면 채점이 진행되지 않기 때문에, 다음과 같은 dummy checker를 사용할 수 있습니다.

```cpp
#include "testlib.h"

int main(int argc, char** argv) {
    registerTestlibCmd(argc, argv);
    while (!ouf.seekEof()) {
        ouf.readToken("[!-~]+", "dummy");
    }
    quitf(_ok, "OK");
    return 0;
}
```

### 문제 예시

> 1 이상 15 이하의 정수를 알아맞혀 보자.
>
> 다음을 한 줄에 출력하여 인터랙터에게 쿼리를 할 수 있다. 쿼리는 최대 4번까지 할 수 있다.
>
> * `n`: 숨겨진 값이 $n$인지 질문한다. ($1 \le n \le 15$)
>
> 쿼리의 결과는 한 줄을 입력받아 알 수 있다. 가능한 결과는 다음과 같다.
>
> * `-1`: 숨겨진 값은 $n$보다 작다.
> * `0`: 숨겨진 값은 $n$과 같다.
> * `1`: 숨겨진 값은 $n$보다 크다.
>
> `0`의 결과를 받았다면 성공적으로 숨겨진 값을 찾은 것이다. 이 경우에는 더 이상 쿼리를 출력하지 않고 즉시 프로그램을 종료해야 한다.

이 문제의 interactor는 다음과 같이 작성할 수 있습니다. 테스트 케이스 파일에는 숨겨진 정수가 들어 있습니다.

```cpp
#include "testlib.h"

int main(int argc, char** argv) {
    registerInteraction(argc, argv);
    int hidden = inf.readInt();
    bool found = false;
    for (int tries = 0; tries < 4; tries++) {
        int guess = ouf.readInt(1, 15);
        if (hidden > guess) {
            cout << 1 << endl;
        } else if (hidden == guess) {
            cout << 0 << endl;
            found = true;
            break;
        } else {
            cout << -1 << endl;
        }
    }
    if (!found) {
        quitf(_wa, "failed to find the hidden number");
    }
    quitf(_ok, "found the hidden number");
    return 0;
}
```

## 참고

- [GitHub: MikeMirzayanov/testlib](https://github.com/MikeMirzayanov/testlib)
- [Polygon 플랫폼](https://polygon.codeforces.com/)
- [Codeforces: Briefly about testlib.h](https://codeforces.com/testlib)
- [Codeforces: Generators with testlib.h](https://codeforces.com/blog/entry/18291)
- [Codeforces: Validators with testlib.h](https://codeforces.com/blog/entry/18426)
- [Codeforces: Interactors with testlib.h](https://codeforces.com/blog/entry/18455)
- [Codeforces: Checkers with testlib.h](https://codeforces.com/blog/entry/18431)
