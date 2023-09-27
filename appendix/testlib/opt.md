# opt

- 해당 내용: **Generator**

testlib.h는 generator 옵션을 조금 더 쉽게 파싱할 수 있게 `opt` 계열 메서드들을 제공합니다. 아래 `opt` 등장 이전의 코드와 `opt` 등장 이후의 코드를 비교하면, 확실히 `opt`를 사용하는 것이 더 간결하고 읽기 쉬운 것을 알 수 있습니다.

- `opt` 등장 이전

```
gen 100000 500 true
```

```cpp
int N = atoi(argv[1]);
int M = atoi(argv[2]);
bool isTree = (strcmp(argv[3], "true") == 0);
```

- `opt` 등장 이후

```
gen -n100000 -m500 --is-tree
```
```cpp
int N = opt<int>("n");
int M = opt<int>("m");
bool isTree = opt<bool>("is-tree");
```

## 형식

`opt`가 지원하는 generator 옵션 형식은 아래 4가지 중 하나입니다. keyName은 알파벳으로 시작해야 합니다. value는 어떤 형식이든 가능하나, 수인 경우 과학적 표기법도 가능합니다.

- `-keyName=value` 혹은 `--keyName=value`
  - `-n=10000`, `--num-segments=1e6`
- `-keyName value` 혹은 `--keyName value`
  - `-n 10000`, `--num-segments 1e6`
- `-kValue` 혹은 `--kValue`
  - keyName은 한 글자 알파벳이어야 하며, 이어지는 value는 수여야 합니다.
  - `-n10000`, `--m1e6`
- `-boolProperty` 혹은 `--boolProperty`
  - `-isDag`, `--is-sorted`

## 메서드

### opt

- `template<typename T> T opt(const std::string &key)`
- `std::string opt(const std::string &key)`
- `T`: 반환할 값의 타입.
- `key`: 옵션의 이름.

옵션 `key`에 해당하는 값을 반환합니다. `opt<bool>`을 제외한 경우에 옵션 `key`를 찾지 못했을 경우, `_fail` 결과로 종료합니다.

`T`에 따라 다른 동작을 합니다.

- `opt("key")`는 `key`를 갖는 옵션을 `std::string`으로 반환합니다.
- `opt<std::string>("key")`의 동작은 `opt("key")`와 같습니다.
- `opt<int>("key")`, `opt<long long>("key")` 등은 옵션 `key`를 정수로 취급하여 파싱합니다. 파싱에 실패하는 경우, `_fail` 결과로 종료합니다. `__int128` 등의 비표준 자료형에서의 동작을 보장하지 않습니다.
- `opt<double>("key")`의 경우 옵션 `key`를 실수로 취급하여 파싱합니다. 파싱에 실패하는 경우, `_fail` 결과로 종료합니다. `__float128` 등의 비표준 자료형에서의 동작을 보장하지 않습니다.
- `opt<bool>("key")`의 경우 옵션 `key`가 존재하고 `true`와 `1` 중 하나의 값을 가지면 `true`를 반환하며, 옵션 `key`가 존재하지 않거나 `false`와 `0` 중 하나의 값을 가지면 `false`를 반환합니다. 이외의 경우 `_fail` 결과로 종료합니다.

### has_opt

- `bool has_opt(const std::string &key)`
- `key`: 옵션의 이름.

옵션 `key`가 존재하는지 여부를 반환합니다.