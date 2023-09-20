# testlib.h 비공식 문서

testlib.h version `0.9.40-SNAPSHOT`

## 목차

- [전역 유틸리티 메서드](./utils.md)
- opt (TODO)
- InStream, inf, ouf, ans (TODO)
- registerGen, registerInteraction (tout), registerValidation, registerTestlibCmd
- [random_t](./random_t.md): 무작위 값 생성 및 전역 변수 `rnd`
- [pattern](./pattern.md): 문자열 매칭 및 표현식.
- TestlibFinalizeGuard (TODO)

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
