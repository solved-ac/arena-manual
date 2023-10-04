# TResult

`enum TResult`는 [`quit`](utils.md#quit) 및 [`quitf`](utils.md#quitf) 등의 메서드에서 사용되는 채점 결과를 나타내는 열거형입니다.

Checker의 일부분인 아래 코드에서 [`_wa`](#_wa)는 `TResult`의 값입니다.

```cpp
quitf(_wa, "Extra tokens found in participant's answer");
```

일반적으로 Validator와 Generator에서는 이 결과를 직접 사용하여 종료하는 경우는 없습니다.

- 참고: [Checker](README.md#checker)
- 참고: [BOJ Help: 채점 결과](https://help.acmicpc.net/judge/info)

## 값 및 각 플랫폼에서 대응되는 값

|        값         | Polygon | BOJ                        | Codeforces         |
| :---------------: | :-----: | :------------------------- | :----------------- |
|   [`_ok`](#_ok)   |   OK    | 맞았습니다!!               | Accepted           |
|   [`_wa`](#_wa)   |   WA    | 틀렸습니다                 | Wrong Answer       |
|   [`_pe`](#_pe)   |   PE    | 출력 형식이 잘못되었습니다 | Presentation Error |
| [`_fail`](#_fail) |   FL    | 틀렸습니다                 | Fail               |

이외의 채점 결과(시간 초과, 메모리 초과, 런타임 에러 등)는 채점기에서 직접 발생시킬 수 없으며, 각 플랫폼의 채점 시스템에서 자동 판정합니다.

부분 점수로 종료하려면 [`quitp`](utils.md#quitp)를 사용합니다.

## 값

### \_ok

- `TResult _ok = 0`

참가자의 출력이 정답임을 나타내는 값.

BOJ에서 이 값은 초록색 **맞았습니다!!**(`4`)에 대응됩니다. Polygon에서 이 값은 **OK**(**OK**)에 대응됩니다.

### \_wa

- `TResult _wa = 1`

참가자의 출력이 오답임을 나타내는 값.

BOJ에서 이 값은 **틀렸습니다**(`6`)에 대응됩니다. Polygon에서 이 값은 **Wrong Answer**(**WA**)에 대응됩니다.

### \_pe

- `TResult _pe = 2`

참가자의 출력이 형식에 맞지 않음을 나타내는 값.

BOJ에서 이 값은 **출력 형식이 잘못되었습니다**(`5`)에 대응됩니다. Polygon에서 이 값은 **Presentation Error**(**PE**)에 대응됩니다.

### \_fail

- `TResult _fail = 3`

참가자의 출력을 바탕으로 채점을 진행할 수 없음을 나타내거나, 채점 중 오류가 발생했음을 나타내는 값.

BOJ에서 이 값은 **틀렸습니다**(`6`)에 대응됩니다. Polygon에서 이 값은 **Fail**(**FL**)에 대응됩니다.

### \_dirt

- `TResult _dirt = 4`

일반적으로 직접 사용하지 않습니다. 내부적으로만 사용되는 값입니다.

참가자의 출력에 불필요한 토큰이 더 남아 있음을 나타내는 값. 채점기가 [`_ok`](#_ok) 혹은 [`quitp`](utils.md#quitp)로 종료했을 때, 참가자의 출력에 토큰이 더 남아 있을 경우 채점기는 자동으로 이 값으로 종료하며, [`_pe`](#_pe)로 종료한 것과 같은 결과를 발생시킵니다.

### \_points

- `TResult _points = 5`

일반적으로 직접 사용하지 않습니다. 내부적으로만 사용되는 값입니다.

참가자가 어떤 점수 값을 획득하였음을 의미하는 값. [`quitp`](utils.md#quitp)로 종료했을 때 내부적으로 사용되는 값입니다.

### \_unexpected_eof

- `TResult _unexpected_eof = 8`

일반적으로 직접 사용하지 않습니다. 내부적으로만 사용되는 값입니다.

참가자의 출력에서 토큰을 더 읽을 수 없음을 나타내는 값. 채점기가 토큰을 더 읽을 수 없을 때, [`_pe`](#_pe)로 종료한 것과 같은 결과를 발생시킵니다.

### \_partially

- `TResult _partially = 16`

예전의 채점 방식에서만 사용되는 값입니다. 현재는 대신 [`quitp`](utils.md#quitp)를 사용하는 것을 권장합니다.

일반적으로 직접 사용하지 않습니다. 내부적으로만 사용되는 값입니다.

참가자가 어떤 점수 값을 획득하였음을 의미하는 값. `quitf(_partially, ...)` 대신 매크로 `_pc`를 이용하여 `quitf(_pc(score), ...)`를 사용하여야 하며, 이때 $0 \le \texttt{score} \le 200$으로, 200점을 만점으로 취급합니다.
