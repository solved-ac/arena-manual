# random_t

- 해당 내용: **Generator**

### rnd

- `random_t rnd`

`rnd`는 `random_t`의 인스턴스로, 전역 변수입니다.

`rnd`의 랜덤 시드는 `registerGen` 시에 결정되며, 커맨드 라인 변수 `argc`와 `argv`를 기반으로 계산됩니다. 즉, 커맨드 라인 변수가 같다면 랜덤의 결과도 항상 같습니다.

문제의 정해를 테스트할 때, 테스트를 생성할 때마다 랜덤 시드가 바뀐다면 stress test 등으로 발견한 예외 케이스가 유실될 수 있습니다. `random_t`를 사용하면 테스트 케이스 생성기를 여러 번 실행해도 같은 테스트 케이스를 생성해 주므로 이런 문제를 방지할 수 있습니다.

제네레이터를 작성할 때에는 이런 이유로 `std::rand`, `std::random_shuffle`과 `std::srand`를 사용하지 않고, 전역 변수 `rnd`를 통해 무작위 값을 생성해야 합니다.

### random_t::setSeed

- `void setSeed(long long _seed)`
- `_seed`: 랜덤 시드.

`random_t`의 시드를 설정합니다. 일반적인 경우 시드는 자동으로 결정되므로 이 메서드를 사용할 일은 없습니다.

## 무작위 생성

### random_t::next(std::string)

- `std::string next(const std::string &ptrn)`
- `std::string next(const char *format, ...)`
- `ptrn`: 정규 표현식.
- `format`, `...`: 정규 표현식을 생성하는 `printf` 스타일의 포맷 문자열 및 가변 인자.

`ptrn`을 만족하는 문자열을 무작위 생성합니다.

`ptrn`은 0회 이상 반복을 나타내는 문자 `*`를 포함하면 안 됩니다. `ptrn`에 어떤 문자를 제외하고 나머지를 허용하는 문법 `[^...]`이 등장할 경우 제대로 실행됨을 보장하지 않습니다.

`next`는 포맷 스트링을 직접 지원합니다. 즉, `next(format("[ABC]{%d}", n))` 대신 `next("[ABC]{%d}", n)`와 같이 작성 가능합니다.

### random_t::next(integer)

- `int next(int n)`
- `unsigned int next(unsigned int n)`
- `long long next(long long n)`
- `unsigned long long next(unsigned long long n)`
- `long next(long n)`
- `unsigned long next(unsigned long n)`
- `n`: 생성 범위를 나타내는 정수.

$\left[0,n\right)$ 범위의 정수를 무작위로 생성합니다. `n`은 양의 정수여야 합니다.

### random_t::next(integer, integer)

- `int next(int from, int to)`
- `unsigned int next(unsigned int from, unsigned int to)`
- `long long next(long long from, long long to)`
- `unsigned long long next(unsigned long long from, unsigned long long to)`
- `long next(long from, long to)`
- `unsigned long next(unsigned long from, unsigned long to)`
- `from`, `to`: 생성 범위를 나타내는 정수.

$\left[\texttt{from},\texttt{to}\right]$ 범위의 정수를 무작위로 생성합니다. 값 `to`도 생성될 수 있음에 주의합니다. $\texttt{from} < \texttt{to}$여야 합니다.

### random_t::next()

- `double next()`

$\left[0, 1\right)$ 사이의 실수를 무작위로 생성합니다.

### random_t::next(double)

- `double next(double n)`
- `n`: 생성 범위를 나타내는 실수.

$\left[0, n\right)$ 사이의 실수를 무작위로 생성합니다. $n>0$이어야 합니다.

### random_t::next(double, double)

- `double next(double from, double to)`
- `from`, `to`: 생성 범위를 나타내는 실수.

$\left[\texttt{from},\texttt{to}\right]$ 범위의 실수를 무작위로 생성합니다. $\texttt{from} < \texttt{to}$여야 합니다.

### random_t::any

- `template<typename Container> typename Container::value_type any(const Container &c)`
- `template<typename Iter> typename Iter::value_type any(const Iter &begin, const Iter &end)`
- `Container`: 컨테이너 타입.
- `Iter`: 반복자 타입.
- `c`: 컨테이너.
- `begin`, `end`: 반복자.

`c`, 혹은 $\left[\texttt{begin}, \texttt{end}\right)$로 정의된 구간 내의 원소 중 하나를 무작위로 선택합니다.

## 가중 무작위 생성

아래 모든 함수는 `next` 메서드와 비슷한 동작을 가지나, 인자 `type`의 값에 따라 조금씩 다른 동작을 보입니다.

- $\texttt{type}=0$이라면, 이 메서드는 `next(...)`과 동작이 같습니다.
- $\texttt{type}>0$이라면, 이 메서드는 `next(...)`을 `type` 번 호출하여, 그 중 가장 큰 값을 반환합니다.
- $\texttt{type}<0$이라면, 이 메서드는 `next(...)`을 `-type` 번 호출하여, 그 중 가장 작은 값을 반환합니다.

### random_t::wnext(type)

- `double wnext(int type)`
- `type`: 생성 방식을 나타내는 정수.

$\left[0, 1\right)$ 사이의 실수를 생성합니다.

### random_t::wnext(double, type)

- `double wnext(double n, int type)`
- `n`: 생성 범위를 나타내는 실수.
- `type`: 생성 방식을 나타내는 정수.

$\left[0, n\right)$ 사이의 실수를 생성합니다. $n>0$이어야 합니다.

### random_t::wnext(integer, type)

- `int wnext(int n, int type)`
- `unsigned int wnext(unsigned int n, int type)`
- `long long wnext(long long n, int type)`
- `unsigned long long wnext(unsigned long long n, int type)`
- `long wnext(long n, int type)`
- `unsigned long wnext(unsigned long n, int type)`
- `n`: 생성 범위를 나타내는 정수.
- `type`: 생성 방식을 나타내는 정수.

$\left[0,n\right)$ 범위의 정수를 생성합니다. `n`은 양의 정수여야 합니다.

### random_t::wnext(integer, integer, type)

- `int wnext(int from, int to, int type)`
- `unsigned int wnext(unsigned int from, unsigned int to, int type)`
- `long long wnext(long long from, long long to, int type)`
- `unsigned long long wnext(unsigned long long from, unsigned long long to, int type)`
- `long wnext(long from, long to, int type)`
- `unsigned long wnext(unsigned long from, unsigned long to, int type)`
- `from`, `to`: 생성 범위를 나타내는 정수.

$\left[\texttt{from},\texttt{to}\right]$ 범위의 정수를 생성합니다. 값 `to`도 생성될 수 있음에 주의합니다. $\texttt{from} < \texttt{to}$여야 합니다.

### random_t::wany

- `template<typename Container> typename Container::value_type wany(const Container &c, int type)`
- `template<typename Iter> typename Iter::value_type wany(const Iter &begin, const Iter &end, int type)`
- `Container`: 컨테이너 타입.
- `Iter`: 반복자 타입.
- `c`: 컨테이너.
- `begin`, `end`: 반복자.
- `type`: 생성 방식을 나타내는 정수.

`c`, 혹은 $\left[\texttt{begin}, \texttt{end}\right)$로 정의된 구간 내의 원소 중 하나를 선택합니다.

`type`가 0이 아닌 경우, 이 함수는 인덱스를 `wnext(c.size(), type)`로 고르고, 그 인덱스에 해당하는 원소를 반환합니다. $|\texttt{type}|$ 번 원소를 고르고 그 중 최대/최솟값을 고르는 것이 아님에 주의합니다.

## 배열 생성

### random_t::perm

- `template<typename T> std::vector<T> perm(T size)`
- `template<typename T, typename E> std::vector<E> perm(T size, E first)`
- `T`: 생성할 배열의 크기를 나타내는 정수 타입.
- `E`: 생성할 배열의 원소를 나타내는 정수 타입.
- `size`: 생성할 배열의 크기.
- `first`: 생성할 배열의 원소의 최솟값.

서로 다른 `size`개의 연속한 수가 무작위 순서로 들어 있는 배열을 생성합니다. `first`가 주어지면, 배열의 원소는 $\left[\texttt{first}, \texttt{first}+\texttt{size}\right)$ 내의 정수를 모두 포함합니다. `first`가 주어지지 않는다면, 배열의 원소는 $\left[0, \texttt{size}\right)$ 내의 정수를 모두 포함합니다.

### random_t::distinct

- `template<typename T> std::vector<T> distinct(int size, T from, T to)`
- `template<typename T> std::vector<T> distinct(int size, T upper)`
- `T`: 생성할 배열의 원소를 나타내는 정수 타입.
- `from`, `to`: 생성할 배열의 원소의 범위.
- `upper`: 생성할 배열의 원소의 최댓값.

주어진 구간 내에서 서로 다른 `size`개의 정수가 들어 있는 배열을 생성합니다. `from`과 `to`가 주어지면, 배열의 원소는 $\left[\texttt{from}, \texttt{to}\right]$ 사이에서 무작위로 생성됩니다. 그렇지 않다면, 배열의 원소는 $\left[0, \texttt{upper}\right)$ 사이에서 무작위로 생성됩니다.

$\texttt{from} < \texttt{to}$; $0 \le \texttt{size} \le \texttt{to}-\texttt{from}+1 \le 10^9$여야 합니다. $\texttt{upper}>0$, $0\le \texttt{size} \le \texttt{upper}$여야 합니다.

### random_t::partition

- `template<typename T> std::vector<T> partition(int size, T sum)`
- `template<typename T> std::vector<T> partition(int size, T sum, T min_part)`
- `T`: 생성할 배열의 원소를 나타내는 정수 타입.
- `size`: 생성할 배열의 크기.
- `sum`: 생성할 배열의 원소의 합.
- `min_part`: 생성할 배열의 원소의 최솟값.

최솟값이 `min_part`이고, 합이 `sum`이 되는 `size`개의 정수가 들어 있는 배열을 생성합니다. `min_part`가 주어지지 않으면, 최솟값은 1입니다.

$\texttt{size} \ge 0$이어야 합니다. $\texttt{size}=0$이라면, $\texttt{sum}=0$이어야 합니다. $\texttt{min\_part}\times\texttt{size}\le\texttt{sum}$이어야 합니다.

## 전역 유틸리티 메서드

### shuffle

- `template<typename _RandomAccessIter> void shuffle(_RandomAccessIter __first, _RandomAccessIter __last)`
- `_RandomAccessIter`: 랜덤 액세스 반복자 타입.
- `__first`: 배열의 시작 반복자.
- `__last`: 배열의 끝 반복자.

배열 $\left[\texttt{\_\_first}, \texttt{\_\_last}\right)$를 무작위 순서로 섞습니다.
