# 전역 유틸리티 메서드

아래 메서드들은 전역으로 정의되며, checker, validator, interactor, generator 모두에서 사용할 수 있습니다.

## 목차

- [목차](#목차)
- [검증](#검증)
  - [ensure](#ensure)
  - [ensuref](#ensuref)
- [채점](#채점)
  - [quit](#quit)
  - [quitp](#quitp)
  - [quitf](#quitf)
  - [quitif](#quitif)
- [실수형](#실수형)
  - [doubleCompare](#doublecompare)
  - [doubleDelta](#doubledelta)
- [문자열](#문자열)
  - [compress](#compress)
  - [format](#format)
  - [isBlanks](#isblanks)
  - [isEof](#iseof)
  - [isEoln](#iseoln)
  - [join](#join)
  - [lowerCase](#lowercase)
  - [split](#split)
  - [toHumanReadableString](#tohumanreadablestring)
  - [tokenize](#tokenize)
  - [toString](#tostring)
  - [trim](#trim)
  - [vtos](#vtos)
  - [upperCase](#uppercase)
- [수와 문자열](#수와-문자열)
  - [englishEnding](#englishending)
  - [equals](#equals)
  - [removeDoubleTrailingZeroes](#removedoubletrailingzeroes)
- [출력](#출력)
  - [println](#println)
- [채점 옵션 처리](#채점-옵션-처리)

## 검증

### ensure

- `#define ensure(cond)`
- `cond`: 참이어야 하는 조건.

`cond`가 `true`일 것을 보장합니다.

자세한 이유를 명시하기 위하여 [`ensuref`](#ensuref)를 사용하는 것이 좋습니다.

### ensuref

- `inline void ensuref(bool cond, const char *format, ...)`
- `cond`: 참이어야 하는 조건.
- `format`, `...`: `printf` 스타일의 포맷 문자열 및 가변 인자.

`cond`가 `true`일 것을 보장합니다.

`cond`가 `false`인 경우, `format`을 포맷 문자열로 사용하여 `printf` 스타일의 메시지를 출력하고 [`_fail`](tresult.md#_fail) 결과로 종료합니다.

## 채점

### quit

- `void quit(TResult result, const std::string &msg)`
- `void quit(TResult result, const char *msg)`
- `result`: 채점 결과.
- `msg`: 메시지.

채점을 `result` 결과로 종료합니다. (참고: [TResult](tresult.md))

포맷 문자열을 사용하고자 하는 경우, [`quitf`](#quitf)를 사용하는 것이 좋습니다.

### quitp

- `void quitp(F points, const char *format, ...)`
- `F`: 점수의 자료형.
- `points`: 채점 결과 참가자가 획득한 점수.
- `format`, `...`: `printf` 스타일의 포맷 문자열 및 가변 인자.

채점을 `points`의 점수를 획득한 것으로 하여 종료합니다.

### quitf

- `void quitf(TResult result, const char *msg, ...)`
- `result`: 채점 결과.
- `msg`, `...`: `printf` 스타일의 포맷 문자열 및 가변 인자.

채점을 `result` 결과로 종료합니다. (참고: [TResult](tresult.md))

### quitif

- `void quitif(bool condition, TResult result, const char *format, ...)`
- `condition`: 채점을 종료할지 여부.
- `result`: 채점 결과.
- `format`, `...`: `printf` 스타일의 포맷 문자열 및 가변 인자.

`condition`이 `true`인 경우, 채점을 `result` 결과로 종료합니다. (참고: [TResult](tresult.md))

## 실수형

### doubleCompare

- `inline bool doubleCompare(double expected, double result, double MAX_DOUBLE_ERROR)`
- `expected`: 기대하는 값.
- `result`: 실제 값.
- `MAX_DOUBLE_ERROR`: 오차의 최댓값.

`expected`와 `result`의 오차가 일정 값 이하인지 확인합니다.

`expected`와 `result`의 절대/상대 오차가 $\left(\texttt{MAX\\\_DOUBLE\\\_ERROR} + 10^{-15}_\mathbb{F}\right)\_\mathbb{F}$ 이하인지 확인합니다.

`expected` 혹은 `result`가 $\text{NaN}$인 경우, 둘 모두 $\text{NaN}$인 경우에만 `true`를 반환합니다. `expected` 혹은 `result`가 $\pm \infty$인 경우, 둘 모두 $\pm \infty$이며 부호가 같은 경우에만 `true`를 반환합니다.

### doubleDelta

- `inline double doubleDelta(double expected, double result)`
- `expected`: 기대하는 값.
- `result`: 실제 값.

`expected`와 `result`의 오차를 계산합니다.

$\texttt{expected} > 10^{-9}_\mathbb{F}$라면, `expected`와 `result`의 절대/상대 오차 중 작은 것을 반환합니다. 그렇지 않다면, `expected`와 `result`의 절대 오차를 반환합니다.

## 문자열

### compress

- `inline std::string compress(const std::string &s)`
- `s`: 문자열.

문자열 `s`에 `\0` = 0이 있다면 전부 `~` = 126으로 변경하고, 64글자 이하가 되도록 합니다.

`s`가 64글자가 넘는다면, `s`의 앞 30글자와 뒤 30글자를 제외한 부분을 `...`으로 대체합니다.

### format

- `std::string format(const char *fmt, ...)`
- `std::string format(const std::string fmt, ...)`
- `fmt`: 형식 문자열. `printf`와 동일한 형식을 사용합니다.

`printf`와 동일한 형식을 사용하는 형식 문자열 `fmt`와 가변 인자를 받아, 형식 문자열에 가변 인자를 적용한 결과를 반환합니다.

- `format("a_%d", 1)` &rarr; `"a_1"`
- `format("a_(%d,%d)", 1, 2)` &rarr; `"a_(1,2)"`

### isBlanks

- `template<typename C> inline bool isBlanks(C c)`
- `C`: 문자의 타입.
- `c`: 문자.

`c`가 공백 문자인지 확인합니다.

`C`는 문자 혹은 정수형이어야 합니다.

### isEof

- `template<typename C> inline bool isEof(C c)`
- `C`: 문자의 타입.
- `c`: 문자.

`c`가 EOF(255)인지 확인합니다.

`C`는 문자 혹은 정수형이어야 합니다.

### isEoln

- `template<typename C> inline bool isEoln(C c)`
- `C`: 문자의 타입.
- `c`: 문자.

`c`가 개행 문자인지 확인합니다.

`C`는 문자 혹은 정수형이어야 합니다.

### join

- `template<typename _Collection> std::string join(const _Collection &collection)`
- `template<typename _Collection, typename _Separator> std::string join(const _Collection &collection, _Separator separator)`
- `template<typename _ForwardIterator> std::string join(_ForwardIterator first, _ForwardIterator last)`
- `template<typename _ForwardIterator, typename _Separator> std::string join(_ForwardIterator first, _ForwardIterator last, _Separator separator)`
- `_Collection`, `_ForwardIterator`: 컬렉션 또는 반복자 타입.
- `_Separator`: 구분자의 타입.
- `collection`: 컬렉션.
- `first`: 컬렉션의 시작 반복자.
- `last`: 컬렉션의 끝 반복자.
- `separator`: 구분자(명시하지 않으면, ` ` = 20).

컬렉션 `collection`의 모든 원소, 또는 반복자 `first`..`last`로 정의된 구간을 구분자 `separator`로 구분하여 문자열로 표현한 값을 반환합니다.

`_Collection`은 `_Collection::begin()`, `_Collection::end()`가 정의되어 있어야 하며, forward 반복자를 반환해야 합니다. `first`와 `last`는 forward 반복자여야 합니다. `_Collection` 및 `_ForwardIterator`의 원소 `T`는 `operator<<(std::stringstream, T)`가 정의되어 있어야 합니다.

`_Seperator`는 `operator<<(std::stringstream, _Separator)`가 정의되어 있어야 합니다.

### lowerCase

- `inline std::string lowerCase(std::string s)`
- `s`: 문자열.

문자열 `s`의 모든 문자를 소문자로 바꾼 문자열을 반환합니다.

### split

- `std::vector<std::string> split(const std::string &s, char separator)`
- `std::vector<std::string> split(const std::string &s, const std::string &separators)`
- `s`: 문자열.
- `separator`: 구분자.
- `separators`: 구분자들.

문자열 `s`를 구분자 `separator` 혹은 여러 구분자들 `separators` 중 하나로 구분하여 문자열의 벡터로 표현한 값을 반환합니다. 구분자가 $k$번 나타난다면, 반환되는 벡터의 크기는 $k+1$입니다. 즉, 빈 문자열이 포함될 수 있습니다.

### toHumanReadableString

- `template<typename T> static std::string toHumanReadableString(const T &n)`
- `template<typename T> static std::string toHumanReadableString(const T &n, std::false_type)`
- `template<typename T> static std::string toHumanReadableString(const T &n, std::true_type)`
- `T`: 포맷할 값의 타입.
- `n`: 포맷할 값.

정수 `n`을 문자열로 표현했을 때 맨 뒤의 0을 축약하여 사람이 읽기 쉬운 형태로 표현한 값을 반환합니다.

맨 뒤의 0의 개수가 7개 이상인 경우에 지수 형태로 표현합니다.

`T`는 정수형이어야 합니다.

- `toHumanReadableString(10000)` &rarr; `"10000"`
- `toHumanReadableString(10_000_000)` &rarr; `"10^7"`
- `toHumanReadableString(31_400_000_000LL)` &rarr; `"314*10^8"`

### tokenize

- `std::vector<std::string> tokenize(const std::string &s, char separator)`
- `std::vector<std::string> tokenize(const std::string &s, const std::string &separators)`

문자열 `s`를 구분자 `separator` 혹은 여러 구분자들 `separators` 중 하나로 구분하고, 이 중 빈 문자열을 제외하여 문자열의 벡터로 표현한 값을 반환합니다.

### toString

- `template<typename T> static std::string toString(const T &t)`

= [vtos](#vtos).

### trim

- `inline std::string trim(const std::string &s)`
- `s`: 문자열.

문자열 `s`의 앞/뒤에 있는 공백 문자를 제거한 문자열을 반환합니다.

### vtos

- `template<typename T> static std::string vtos(const T &t)`
- `template<typename T> static std::string vtos(const T &t, std::true_type)`
- `template<typename T> static std::string vtos(const T &t, std::false_type)`
- `T`: 포맷할 값의 타입.
- `t`: 포맷할 값.

`t`를 문자열로 표현한 값을 반환합니다.

`T`는 정수형이거나, `operator<<(std::stringstream, T)`가 정의되어 있는 타입이어야 합니다.

### upperCase

- `inline std::string upperCase(std::string s)`
- `s`: 문자열.

문자열 `s`의 모든 문자를 대문자로 바꾼 문자열을 반환합니다.

## 수와 문자열

### englishEnding

- `inline std::string englishEnding(int x)`
- `x`: 정수.

`x`의 영어 서수 표현에서의 접미사를 반환합니다. 음수에 대해서는 제대로 된 동작을 보장하지 않습니다.

- `englishEnding(1)` &rarr; `"st"`
- `englishEnding(12)` &rarr; `"th"`
- `englishEnding(23)` &rarr; `"rd"`

### equals

- `static inline bool equals(long long integer, const char *s)`
- `static inline bool equals(unsigned long long integer, const char *s)`
- `integer`: 정수.
- `s`: 문자열.

`integer`를 문자열로 표현한 값이 `s`와 일치하는지 확인합니다.

`s` 앞의 0은 무시하지 않으며, `integer`가 음수인 경우에는 부호를 포함하여 일치하는지 확인합니다.

- `equals(123, "123")` &rarr; `true`
- `equals(123, "0123")` &rarr; `false`
- `equals(-123, "-123")` &rarr; `true`

### removeDoubleTrailingZeroes

- `static std::string removeDoubleTrailingZeroes(std::string value)`
- `value`: 실수를 표현하는 문자열.

실수를 표현하는 문자열 `value`의 끝에 있는 0을 제거한 값을 반환합니다.

원래 문자열에 소수점이 없었다면 새로 추가하지 않습니다. 원래 문자열의 마지막 문자가 소수점이었다면, 맨 뒤에 `0`을 추가합니다.

- `removeDoubleTrailingZeroes("1.000000")` &rarr; `"1.0"`
- `removeDoubleTrailingZeroes("1.")` &rarr; `"1.0"`

## 출력

### println

- `template<typename T> void println(const T &x)`
- `template<typename A> void println(const A *a, const A *b)`
- `template<typename A, typename B> typename __testlib_enable_if<!is_iterator<B>::value, void>::type println(const A &a, const B &b)`
- `template<typename A, typename B> typename __testlib_enable_if<is_iterator<B>::value, void>::type println(const A &a, const B &b)`
- `template<> void println<char>(const char *a, const char *b)`
- `template<typename A, typename B, typename C> void println(const A &a, const B &b, const C &c)`
- `template<typename A, typename B, typename C, typename D> void println(const A &a, const B &b, const C &c, const D &d)`
- `template<typename A, typename B, typename C, typename D, typename E> void println(const A &a, const B &b, const C &c, const D &d, const E &e)`
- `template<typename A, typename B, typename C, typename D, typename E, typename F> void println(const A &a, const B &b, const C &c, const D &d, const E &e, const F &f)`
- `template<typename A, typename B, typename C, typename D, typename E, typename F, typename G> void println(const A &a, const B &b, const C &c, const D &d, const E &e, const F &f, const G &g)`
- `A`, `B`, `C`, `D`, `E`, `F`, `G`: 출력할 값의 타입.
- `a`, `b`, `c`, `d`, `e`, `f`, `g`: 출력할 값.

여러 값을 한 줄으로 하여 표준 출력 스트림으로 출력합니다. 인자는 7개까지 받을 수 있습니다. 줄 끝에 개행 문자를 출력하며, 출력 버퍼를 비웁니다(flush).

특별하게, 인자가 2개인 경우이면서 두 인자의 타입이 문자열 포인터가 아닌 포인터라면, 첫 번째 인자를 `begin`, 두 번째 인자를 `end`로 간주하여 `begin`..`end`의 구간에 있는 원소들을 출력합니다. 문자열 포인터의 경우에는 문자열 두 개를 출력합니다.

또한, 인자가 2개인 경우이면서 두 인자가 모두 반복자라면, 마찬가지로 첫 번째 인자를 `begin`, 두 번째 인자를 `end`로 간주하여 `begin`..`end`의 구간에 있는 원소들을 출력합니다.

`A`, `B`, `C`, `D`, `E`, `F`, `G`는 `operator<<(std::stringstream, T)`가 정의되어 있어야 합니다. 또한, 인자가 2개인 경우 중 앞서 언급한 특별한 경우에 대해서, 반복 또는 포인터가 가리키는 값 `T`에 대해서도 `operator<<(std::stringstream, T)`가 정의되어 있어야 합니다.

## 채점 옵션 처리

&rarr; [opt](./opt.md)
