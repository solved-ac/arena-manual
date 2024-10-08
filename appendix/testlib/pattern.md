# pattern

`pattern`은 정규 표현식과 비슷한 문법의 오토마타 표현식을 처리하는 클래스입니다. 다음 문법이 지원됩니다.

- `Alice|Bob`: `Alice` 혹은 `Bob`.
- `A?`: `A`가 0번 혹은 1번 등장.
- `A*`: `A`가 0번 이상 등장. (generation에서는 사용 불가)
- `A+`: `A`가 1번 이상 등장.
- `A{m}`: `A`가 정확히 `m`번 등장.
- `A{m,n}`: `A`가 `m`번 이상, `n`번 이하 등장.
- `[a-z]`: 소문자 `a`부터 `z`까지의 문자 중 하나.
- `[ABC]`: `A`, `B`, `C` 중 하나.
- `[^ABC]`: `A`, `B`, `C`를 제외한 문자 중 하나. (generation에서는 사용이 권장되지 않음)
- `()`: 그룹.

특수 문자는 `\`로 escape할 수 있습니다.

다음은 몇 가지 `pattern`의 예입니다.

- `[Yy][Ee][Ss]|[Nn][Oo]`: 대소문자 구별 없이, `yes` 혹은 `no`.
- `[ABC]{1,20}`: `A`, `B`, `C`만으로 구성된 길이 1 이상 20 이하의 문자열.
- `0|-?[1-9][0-9]{0,99}`: 100글자 미만의 정수.

길이 $N$의 `pattern` 등을 생성할 때에는 [`format`](./utils.md#format)을 사용하는 것이 좋습니다.

- `format("[AB]{%d}", N)`: `A` 또는 `B`만으로 구성된 길이 $N$의 문자열.

같은 `pattern`을 자주 사용한다면, 일반적인 정규식과 비슷하게 `pattern p("[0-9]+")` 등으로 미리 정의해 두고 사용하는 것이 실행 시간 면에서 더 효율적입니다.

## 일반적인 정규식과 다른 점

`pattern`이 지원하는 표현식은 일반적인 정규식과 약간의 차이점이 존재합니다.

- 모든 문자를 매칭하는 `.` 연산자는 지원하지 않습니다.
- 띄어쓰기 ` `는 무시합니다. 표현식 `No solution`은 `Nosolution`을 매칭합니다. 띄어쓰기 문자 자체를 매칭하고 싶다면, `\ `를 사용해야 합니다.
- `[^ABC]`는 일반 정규식에서는 `A`, `B`, `C`를 제외한 모든 문자를 매칭하지만, `pattern`에서는 `\0` = 0부터 `þ` = 254까지의 문자 중 `A`, `B`, `C`를 제외한 문자를 매칭합니다. 그러므로 generator에서 `[^ABC]`를 사용하는 것은 권장되지 않습니다.
- 매칭 알고리즘은 그리디합니다. 예를 들어, 패턴 `[0-9]?1`에 `1`을 매칭시키는 경우, 두 번째 문제를 고려하지 않은 채로 첫 번째 문자를 `[0-9]?`에 매칭할 수 있으면 그렇게 합니다. 그러므로, `pattern` 표현식에서 `1`은 `[0-9]?1`에 매칭되지 않습니다.
- `|` 연산자의 매칭에는 브루트포싱 알고리즘을 사용합니다.

별개로, 현재 버전에서 `|`와 `()`를 섞어 쓰는 경우에서 표현식이 잘 동작하지 않는 경우가 발견되고 있습니다. 따라서 `()`는 피해서 사용하는 것을 권장하며, 조금 더 포괄적인 표현식으로 입력받은 후 `std::regex`와 [`ensuref`](utils.md#ensuref)로 검증하는 것을 권장합니다.

## 메서드

### pattern::pattern

- `pattern(std::string s)`
- `s`: 표현식.

`pattern`을 생성합니다.

### pattern::src

- `std::string pattern::src() const`

`pattern`에 사용된 표현식을 반환합니다.

### pattern::matches

- `bool pattern::matches(const std::string& s) const`
- `s`: 매칭할 문자열.

`s`가 `pattern`에 매칭되는지 여부를 반환합니다.

### pattern::next

- `std::string pattern::next(random_t &rnd) const`

= [random_t::next(std::string)](./random_t.md#random_tnextstdstring)
