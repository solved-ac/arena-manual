# InStream

- 해당 내용: **Validator**, **Checker**, **Interactor**

`InStream`은 입력과 출력에 대한 검증 등을 쉽게 할 수 있는 여러 유틸리티 함수를 제공합니다.

## inf, ouf, ans

- `InStream inf`: Validator, checker, interactor에서 테스트 케이스 파일.
- `InStream ouf`: Checker, interactor에서 참가자의 출력.
- `InStream ans`: Checker, interactor에서 출제자의 답안.

`inf`, `ouf`, `ans`는 `InStream`의 인스턴스로, 전역 변수입니다.

## 주요 입력 함수

| 형식                        | 타입                 | 한 개 입력                                                           | 여러 개 입력                                    |
| --------------------------- | -------------------- | -------------------------------------------------------------------- | ----------------------------------------------- |
| 토큰 (공백으로 구분됨)      | `std::string`        | [readToken](#instreamreadtoken), [readTokenTo](#instreamreadtokento) | [readTokens](#instreamreadtokens)               |
| 줄 (공백을 무시하지 않음)   | `std::string`        | [readLine](#instreamreadline)                                        | [readLines](#instreamreadlines)                 |
| 32비트 정수                 | `int`                | [readInt](#instreamreadint)                                          | [readInts](#instreamreadints)                   |
| 64비트 정수                 | `long long`          | [readLong](#instreamreadlong)                                        | [readLongs](#instreamreadlongs)                 |
| 부호 없는 64비트 정수       | `unsigned long long` | [readUnsignedLong](#instreamreadunsignedlong)                        | [readUnsignedLongs](#instreamreadunsignedlongs) |
| 실수 (과학적 표기법 허용)   | `double`             | [readDouble](#instreamreaddouble)                                    | [readDoubles](#instreamreaddoubles)             |
| 실수 (과학적 표기법 비허용) | `double`             | [readStrictDouble](#instreamreadstrictdouble)                        | [readStrictDoubles](#instreamreadstrictdoubles) |

## Strict mode

Strict mode는 입력 형식을 조금 더 엄밀하게 검사하는 모드입니다. 공백 문자와 개행 문자가 나타나는 경우, 정확히 읽어야 합니다.

Strict mode는 **validator의 `inf`에만 적용**되며, validator가 아닌 경우(예를 들어 checker)의 `inf`에는 적용되지 않고, `ouf`과 `ans`에는 어떤 모드일 때에도 적용되지 않습니다.

다음 코드를 예로 들겠습니다. 위는 입력 데이터, 아래는 testlib.h를 사용하는 코드입니다.

```
3 1 4 1 5
```

```cpp
for (int i = 0; i < 5; i++)
    inf.readInt();
```

Strict mode가 꺼져 있다면, testlib.h는 5개의 정수를 성공적으로 읽습니다. Strict mode가 켜져 있다면, 다음과 같은 오류가 발생하며 종료됩니다.

```cpp
quit(_pe, "Unexpected white-space - token expected");
```

Strict mode에서 위 데이터를 올바르게 읽는 코드는 다음과 같습니다.

```cpp
for (int i = 0; i < 5; i++) {
    if (i != 0) inf.readSpace();
    inf.readInt();
}
inf.readEoln();
inf.readEof();
```

Validator의 경우 출제진이 생성한 입력은 위와 같이 검증해야 합니다. Checker에서 참가자의 입력을 검증할 때에는 위와 같이 하지 않아도 괜찮으며, 위와 같이 하지 않는 것이 오히려 권장됩니다. (TODO: Validator와 Checker에 대한 링크 걸기)

## 일반

### InStream::skipBlanks

- `void skipBlanks()`

현재 위치 이후의 연속된 공백 문자들을 모두 무시하고, 공백 문자가 아닌 첫 위치로 커서를 이동합니다.

### InStream::curChar

- `char curChar()`

현재 위치의 문자를 반환합니다. 커서는 이동하지 않습니다.

### InStream::skipChar

- `void skipChar()`

커서를 한 단계 뒤로 이동합니다. 공백을 무시하지 않습니다.

### InStream::readChar

- `char readChar()`
- `char readChar(char c)`
- `c`: 읽을 문자.

현재 위치의 문자를 읽습니다. 커서는 한 단계 뒤로 이동합니다. 공백을 무시하지 않으며, 현재 위치에 공백이 있을 경우 공백을 그대로 읽습니다.

`c`가 주어진 경우, 읽은 문자가 `c`가 아닐 경우 `_pe` 결과로 종료합니다.

### InStream::nextChar

- `char nextChar()`

= [readChar()](#instreamnextchar).

### InStream::readSpace

- `void readSpace()`

= [readChar(`' '`)](#instreamreadchar)

### InStream::unreadChar

- `void unreadChar(char c)`
- `c`: 되돌릴 문자.

커서를 한 단계 앞으로 이동하면서, `c`를 현재 위치에 삽입합니다. 공백을 무시하지 않습니다.

### InStream::eof

- `bool eof()`

현재 위치가 EOF인지 확인합니다. 커서는 이동하지 않습니다.

### InStream::seekEof

- `book seekEof()`

공백 문자가 아닌 첫 위치로 커서를 이동하고, 현재 위치가 EOF인지 확인합니다.

### InStream::eoln

- `bool eoln()`

현재 위치에 개행 시퀀스가 있는지 확인합니다. 현재 위치에 개행 시퀀스가 있다면, 개행 문자 이후의 첫 문자로 커서를 이동하고, 그렇지 않다면 커서는 이동하지 않습니다. 공백을 무시하지 않습니다. 개행 시퀀스는 `\r\n` = 13 10 혹은 `\n` = 10 중 하나입니다.

단, Strict mode에서는 Windows 환경일 경우에는 `\r\n`만을 개행 시퀀스로 보며, 그렇지 않은 환경일 경우에는 `\n`만을 개행 시퀀스로 봅니다.

### InStream::seekEoln

- `bool seekEoln()`

공백 문자가 아닌 첫 위치로 커서를 이동하고, 현재 위치에 개행 시퀀스가 있는지 확인합니다.

### InStream::nextLine

- `void nextLine()`

다음 행의 첫 문자 위치로 커서를 이동합니다.

## 토큰

### InStream::readToken

- `std::string readToken()`
- `std::string readToken(const std::string &ptrn, const std::string &variableName = "")`
- `std::string readToken(const pattern &p, const std::string &variableName = "")`
- `ptrn`, `p`: 입력 검증에 사용할 정규 표현식.
- `variableName`: 변수 이름.

공백 문자로 구분된 토큰을 읽습니다. 공백은 무시합니다. 커서는 토큰의 마지막 문자 이후로 이동합니다.

`ptrn` 혹은 `p`를 명시하는 경우, 명시된 [정규 표현식](./pattern.md)을 만족하는 토큰을 읽습니다. 정규 표현식에 공백 문자 리터럴(`\ ` 등)이 있을 경우 의도한 대로 동작하지 않을 수 있습니다. 만약 읽은 토큰이 정규 표현식을 만족하지 않는다면 `_wa` 결과로 종료합니다.

Strict mode에서는 공백을 무시하지 않으며, 현재 위치가 공백임에도 불구하고 `readToken`을 수항하는 경우 `_pe` 결과로 종료합니다.

Validator에서 `inf`의 입력을 검증하는 경우, `ptrn`, `p`와 `variableName`을 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

### InStream::readTokens

- `std::vector<std::string> readTokens(int size, int indexBase = 1)`
- `std::vector<std::string> readTokens(int size, const std::string &ptrn, const std::string &variablesName = "", int indexBase = 1)`
- `std::vector<std::string> readTokens(int size, const pattern &p, const std::string &variablesName = "", int indexBase = 1)`
- `size`: 읽을 토큰의 개수.
- `ptrn`, `p`: 입력 검증에 사용할 정규 표현식.
- `variablesName`: 변수 이름.
- `indexBase`: 입력 오류 시 출력할 인덱스의 시작 값.

공백으로 구분된 토큰 `size`개를 읽습니다. 토큰과 토큰 사이에 공백이 여러 개 있어도 되며, 이 경우 연속된 여러 개의 공백을 하나의 구분자로 봅니다. 즉, 반환된 벡터에는 빈 문자열이 들어 있지 않습니다. 커서는 `size`번째 토큰의 마지막 문자 이후로 이동합니다.

`ptrn` 혹은 `p`를 명시하는 경우, 명시된 [정규 표현식](./pattern.md)을 만족하는 토큰을 읽습니다. 정규 표현식에 공백 리터럴(`\ ` 등)이 있을 경우 의도한 대로 동작하지 않을 수 있습니다. 만약 읽은 토큰이 정규 표현식을 만족하지 않는다면 `_wa` 결과로 종료합니다.

Strict mode에서는 공백을 무시하지 않으며, 토큰과 토큰 사이에는 무조건 한 개의 띄어쓰기(` ` = 32)가 있어야 하고, 띄어쓰기 이외의 공백 문자는 허용되지 않습니다. 현재 위치가 공백 문자임에도 불구하고 `readTokens`을 수행하는 경우, 또는 토큰이 띄어쓰기 한 개보다 많은 수의 공백 문자로 구분된 경우 `_pe` 결과로 종료합니다.

Validator에서 `inf`의 입력을 검증하는 경우, `ptrn`, `p`와 `variableNames`를 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

### InStream::readTokenTo

- `void readTokenTo(std::string &result)`
- `void readTokenTo(std::string &result, const pattern &p, const std::string &variableName = "")`
- `void readTokenTo(std::string &result, const std::string &ptrn, const std::string &variableName = "")`
- out `result`: 읽은 토큰.
- `ptrn`, `p`: 입력 검증에 사용할 정규 표현식.
- `variableName`: 변수 이름.

[readToken](#instreamreadtoken)과 같은 동작으로 토큰을 읽습니다. 단, 읽은 토큰은 반환되는 대신 `result`에 저장됩니다.

Validator에서 `inf`의 입력을 검증하는 경우, `ptrn`, `p`와 `variableName`을 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

### InStream::readWord

- `std::string readWord()`
- `std::string readWord(const std::string &ptrn, const std::string &variableName = "")`
- `std::string readWord(const pattern &p, const std::string &variableName = "")`

= [readToken](#instreamreadtoken).

### InStream::readWords

- `std::vector<std::string> readWords(int size, int indexBase = 1)`
- `std::vector<std::string> readWords(int size, const std::string &ptrn, const std::string &variablesName = "", int indexBase = 1)`
- `std::vector<std::string> readWords(int size, const pattern &p, const std::string &variablesName = "", int indexBase = 1)`

= [readTokens](#instreamreadtokens).

### InStream::readWordTo

- `void readWordTo(std::string &result)`
- `void readWordTo(std::string &result, const std::string &ptrn, const std::string &variableName = "")`
- `void readWordTo(std::string &result, const pattern &p, const std::string &variableName = "")`

= [readTokenTo](#instreamreadtokento).

### InStream::readLine

- `std::string readLine()`
- `std::string readLine(const std::string &ptrn, const std::string &variableName = "")`
- `std::string readLine(const pattern &p, const std::string &variableName = "")`
- `ptrn`, `p`: 입력 검증에 사용할 정규 표현식.
- `variableName`: 변수 이름.

현재 위치부터 다음 개행 시퀀스 이전까지의 문자열을 읽습니다. 공백을 무시하지 않습니다. 개행 시퀀스는 읽지 않으며, 커서는 개행 시퀀스의 첫 문자 위치로 이동합니다.

`ptrn` 혹은 `p`를 명시하는 경우, 명시된 [정규 표현식](./pattern.md)을 만족하는 토큰을 읽습니다. 정규 표현식에 개행 문자 리터럴(`\n` 등)이 있을 경우 의도한 대로 동작하지 않을 수 있습니다. 만약 읽은 토큰이 정규 표현식을 만족하지 않는다면 `_wa` 결과로 종료합니다.

Strict mode에서는 개행 시퀀스는 여전히 읽지 않지만, 커서는 개행 시퀀스 **이후**의 첫 문자 위치로 이동합니다. Strict mode에서는 Windows 환경일 경우에는 `\r\n`만을 개행 시퀀스로 보며, 그렇지 않은 환경일 경우에는 `\n`만을 개행 시퀀스로 봅니다.

Validator에서 `inf`의 입력을 검증하는 경우, `ptrn`, `p`와 `variableName`을 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

### InStream::readLines

- `std::vector<std::string> readLines(int size, int indexBase = 1)`
- `std::vector<std::string> readLines(int size, const std::string &ptrn, const std::string &variablesName = "", int indexBase = 1)`
- `std::vector<std::string> readLines(int size, const pattern &p, const std::string &variablesName = "", int indexBase = 1)`
- `size`: 읽을 토큰의 개수.
- `ptrn`, `p`: 입력 검증에 사용할 정규 표현식.
- `variablesName`: 변수 이름.
- `indexBase`: 입력 오류 시 출력할 인덱스의 시작 값.

현재 위치부터 개행 시퀀스로 구분된 `size`개의 문자열, 즉 지금 위치로부터 `size`개 줄을 읽습니다. 공백을 무시하지 않습니다. 개행 시퀀스는 읽지 않으며, 커서는 마지막 개행 시퀀스의 첫 문자 위치로 이동합니다.

`ptrn` 혹은 `p`를 명시하는 경우, 명시된 [정규 표현식](./pattern.md)을 만족하는 토큰을 읽습니다. 정규 표현식에 개행 문자 리터럴(`\n` 등)이 있을 경우 의도한 대로 동작하지 않을 수 있습니다. 만약 읽은 토큰이 정규 표현식을 만족하지 않는다면 `_wa` 결과로 종료합니다.

Strict mode에서는 개행 시퀀스는 여전히 읽지 않지만, 커서는 마지막 개행 시퀀스 **이후**의 첫 문자 위치로 이동합니다. Strict mode에서는 Windows 환경일 경우에는 `\r\n`만을 개행 시퀀스로 보며, 그렇지 않은 환경일 경우에는 `\n`만을 개행 시퀀스로 봅니다.

Validator에서 `inf`의 입력을 검증하는 경우, `ptrn`, `p`와 `variableNames`를 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

### InStream::readLineTo

- `void readLineTo(std::string &result)`
- `void readLineTo(std::string &result, const std::string &ptrn, const std::string &variableName = "")`
- `void readLineTo(std::string &result, const pattern &p, const std::string &variableName = "")`
- out `result`: 읽은 토큰.
- `ptrn`, `p`: 입력 검증에 사용할 정규 표현식.
- `variableName`: 변수 이름.

[readLine](#instreamreadline)과 같은 동작으로 토큰을 읽습니다. 단, 읽은 토큰은 반환되는 대신 `result`에 저장됩니다.

Validator에서 `inf`의 입력을 검증하는 경우, `ptrn`, `p`와 `variableName`을 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

### InStream::readString

- `std::string readString()`
- `std::string readString(const std::string &ptrn, const std::string &variableName = "")`
- `std::string readString(const pattern &p, const std::string &variableName = "")`

= [readLine](#instreamreadline).

### InStream::readStrings

- `std::vector<std::string> readStrings(int size, int indexBase = 1)`
- `std::vector<std::string> readStrings(int size, const std::string &ptrn, const std::string &variablesName = "", int indexBase = 1)`
- `std::vector<std::string> readStrings(int size, const pattern &p, const std::string &variablesName = "", int indexBase = 1)`

= [readLines](#instreamreadlines).

### InStream::readStringTo

- `void readStringTo(std::string &result)`
- `void readStringTo(std::string &result, const std::string &ptrn, const std::string &variableName = "")`
- `void readStringTo(std::string &result, const pattern &p, const std::string &variableName = "")`

= [readLineTo](#instreamreadlineto).

## 정수

정수는 0을 제외하고서는 `0`으로 시작할 수 없습니다. 음수의 경우에도 `-0`으로 시작할 수 없습니다. 십진수 표현만 허용되며, 과학적 표기법, 예를 들어 `1e8` 등은 허용되지 않습니다. 만약 읽은 정수가 명시한 제한을 만족하지 않는다면 `_pe` 결과로 종료합니다.

### InStream::readInt

- `int readInt()`
- `int readInt(int minv, int maxv, const std::string &variableName = "")`
- `minv`, `maxv`: 읽을 정수의 범위.
- `variableName`: 변수 이름.

공백 문자로 구분된 32비트 십진 정수를 읽습니다. 공백은 무시합니다. 커서는 정수의 마지막 문자 이후로 이동합니다.

`minv`와 `maxv`를 명시하는 경우, $\texttt{minv} \le x \le \texttt{maxv}$를 만족하는 32비트 십진 정수 $x$를 읽습니다. 만약 읽은 정수가 명시한 범위를 만족하지 않는다면 `_wa` 결과로 종료합니다.

Strict mode에서는 공백을 무시하지 않으며, 현재 위치가 공백임에도 불구하고 `readInt`을 수항하는 경우 `_pe` 결과로 종료합니다.

Validator에서 `inf`의 입력을 검증하는 경우, `minv`, `maxv`와 `variableName`을 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

(TODO: InStream 메서드 설명)

### InStream::readInts

- `std::vector<int> readInts(int size, int indexBase = 1)`
- `std::vector<int> readInts(int size, int minv, int maxv, const std::string &variablesName = "", int indexBase = 1)`
- `size`: 읽을 정수의 개수.
- `minv`, `maxv`: 읽을 정수들의 범위.
- `variablesName`: 변수 이름.
- `indexBase`: 입력 오류 시 출력할 인덱스의 시작 값.

공백으로 구분된 32비트 정수 `size`개를 읽습니다. 정수와 정수 사이에 공백이 여러 개 있어도 되며, 이 경우 연속된 여러 개의 공백을 하나의 구분자로 봅니다. 커서는 `size`번째 정수의 마지막 문자 이후로 이동합니다.

`minv`와 `maxv`를 명시하는 경우, $\texttt{minv} \le x \le \texttt{maxv}$를 만족하는 32비트 십진 정수 $x$를 `size`개 읽습니다. 만약 읽은 정수들 중 하나라도 명시한 범위를 만족하지 않는다면 `_wa` 결과로 종료합니다.

Strict mode에서는 공백을 무시하지 않으며, 토큰과 토큰 사이에는 무조건 한 개의 띄어쓰기(` ` = 32)가 있어야 하고, 띄어쓰기 이외의 공백 문자는 허용되지 않습니다. 현재 위치가 공백 문자임에도 불구하고 `readInts`을 수행하는 경우, 또는 토큰이 띄어쓰기 한 개보다 많은 수의 공백 문자로 구분된 경우 `_pe` 결과로 종료합니다.

Validator에서 `inf`의 입력을 검증하는 경우, `minv`, `maxv`와 `variableName`을 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

### InStream::readInteger

- `int readInteger()`
- `int readInteger(int minv, int maxv, const std::string &variableName = "")`

= [readInt](#instreamreadint).

### InStream::readIntegers

- `std::vector<int> readIntegers(int size, int indexBase = 1)`
- `std::vector<int> readIntegers(int size, int minv, int maxv, const std::string &variablesName = "", int indexBase = 1)`

= [readInts](#instreamreadints).

### InStream::readLong

- `long long readLong()`
- `long long readLong(long long minv, long long maxv, const std::string &variableName = "")`
- `minv`, `maxv`: 읽을 정수의 범위.
- `variableName`: 변수 이름.

공백 문자로 구분된 64비트 십진 정수를 읽습니다. 공백은 무시합니다. 커서는 정수의 마지막 문자 이후로 이동합니다.

`minv`와 `maxv`를 명시하는 경우, $\texttt{minv} \le x \le \texttt{maxv}$를 만족하는 64비트 십진 정수 $x$를 읽습니다. 만약 읽은 정수가 명시한 범위를 만족하지 않는다면 `_wa` 결과로 종료합니다.

Strict mode에서는 공백을 무시하지 않으며, 현재 위치가 공백임에도 불구하고 `readLong`을 수항하는 경우 `_pe` 결과로 종료합니다.

Validator에서 `inf`의 입력을 검증하는 경우, `minv`, `maxv`와 `variableName`을 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

### InStream::readLongs

- `std::vector<long long> readLongs(int size, int indexBase = 1)`
- `std::vector<long long> readLongs(int size, long long minv, long long maxv, const std::string &variablesName = "", int indexBase = 1)`
- `size`: 읽을 정수의 개수.
- `minv`, `maxv`: 읽을 정수들의 범위.
- `variablesName`: 변수 이름.
- `indexBase`: 입력 오류 시 출력할 인덱스의 시작 값.

공백으로 구분된 64비트 정수 `size`개를 읽습니다. 정수와 정수 사이에 공백이 여러 개 있어도 되며, 이 경우 연속된 여러 개의 공백을 하나의 구분자로 봅니다. 커서는 `size`번째 정수의 마지막 문자 이후로 이동합니다.

`minv`와 `maxv`를 명시하는 경우, $\texttt{minv} \le x \le \texttt{maxv}$를 만족하는 64비트 십진 정수 $x$를 `size`개 읽습니다. 만약 읽은 정수들 중 하나라도 명시한 범위를 만족하지 않는다면 `_wa` 결과로 종료합니다.

Strict mode에서는 공백을 무시하지 않으며, 토큰과 토큰 사이에는 무조건 한 개의 띄어쓰기(` ` = 32)가 있어야 하고, 띄어쓰기 이외의 공백 문자는 허용되지 않습니다. 현재 위치가 공백 문자임에도 불구하고 `readLongs`을 수행하는 경우, 또는 토큰이 띄어쓰기 한 개보다 많은 수의 공백 문자로 구분된 경우 `_pe` 결과로 종료합니다.

Validator에서 `inf`의 입력을 검증하는 경우, `minv`, `maxv`와 `variableName`을 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

### InStream::readUnsignedLong

- `unsigned long long readUnsignedLong()`
- `unsigned long long readUnsignedLong(unsigned long long minv, unsigned long long maxv, const std::string &variableName = "")`
- `minv`, `maxv`: 읽을 정수의 범위.
- `variableName`: 변수 이름.

공백 문자로 구분된 부호 없는 64비트 십진 정수를 읽습니다. 공백은 무시합니다. 커서는 정수의 마지막 문자 이후로 이동합니다.

`minv`와 `maxv`를 명시하는 경우, $\texttt{minv} \le x \le \texttt{maxv}$를 만족하는 부호 없는 64비트 십진 정수 $x$를 읽습니다. 만약 읽은 정수가 명시한 범위를 만족하지 않는다면 `_wa` 결과로 종료합니다.

Strict mode에서는 공백을 무시하지 않으며, 현재 위치가 공백임에도 불구하고 `readUnsignedLong`을 수항하는 경우 `_pe` 결과로 종료합니다.

Validator에서 `inf`의 입력을 검증하는 경우, `minv`, `maxv`와 `variableName`을 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

### InStream::readUnsignedLongs

- `std::vector<unsigned long long> readUnsignedLongs(int size, int indexBase = 1)`
- `std::vector<unsigned long long> readUnsignedLongs(int size, unsigned long long minv, unsigned long long maxv, const std::string &variablesName = "", int indexBase = 1)`
- `size`: 읽을 정수의 개수.
- `minv`, `maxv`: 읽을 정수들의 범위.
- `variablesName`: 변수 이름.
- `indexBase`: 입력 오류 시 출력할 인덱스의 시작 값.

공백으로 구분된 부호 없는 64비트 정수 `size`개를 읽습니다. 정수와 정수 사이에 공백이 여러 개 있어도 되며, 이 경우 연속된 여러 개의 공백을 하나의 구분자로 봅니다. 커서는 `size`번째 정수의 마지막 문자 이후로 이동합니다.

`minv`와 `maxv`를 명시하는 경우, $\texttt{minv} \le x \le \texttt{maxv}$를 만족하는 부호 없는 64비트 십진 정수 $x$를 `size`개 읽습니다. 만약 읽은 정수들 중 하나라도 명시한 범위를 만족하지 않는다면 `_wa` 결과로 종료합니다.

Strict mode에서는 공백을 무시하지 않으며, 토큰과 토큰 사이에는 무조건 한 개의 띄어쓰기(` ` = 32)가 있어야 하고, 띄어쓰기 이외의 공백 문자는 허용되지 않습니다. 현재 위치가 공백 문자임에도 불구하고 `readUnsignedLongs`을 수행하는 경우, 또는 토큰이 띄어쓰기 한 개보다 많은 수의 공백 문자로 구분된 경우 `_pe` 결과로 종료합니다.

Validator에서 `inf`의 입력을 검증하는 경우, `minv`, `maxv`와 `variableName`을 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

## 실수

실수 입력 함수들은 공통적으로 실수를 입력받은 후 가장 가까운 `double` 값으로 반올림합니다. 따라서, 아래와 같은 코드를 작성하더라도 입력받은 수의 정확도는 `double`임에 주의합니다.

```cpp
long double x = readDouble();
```

### InStream::readDouble

- `double readDouble()`
- `double readDouble(double minv, double maxv, const std::string &variableName = "")`
- `minv`, `maxv`: 읽을 실수의 범위.
- `variableName`: 변수 이름.

공백 문자로 구분된 실수를 읽습니다. 공백은 무시합니다. 커서는 실수의 마지막 문자 이후로 이동합니다.

실수는 C의 `%lf`로 입력받을 수 있는 형식 중 숫자가 한 개 이상 등장하는 형식만을 허용합니다. 과학적 표기법, 예를 들어 `3.1415e2` 등은 **허용됩니다**. 실수의 형식이 올바르지 않다면 `_pe` 결과로 종료합니다. 허용되는 예는 다음과 같습니다.

- `2`
- `3.14`
- `-1.8e6`
- `.005`

`minv`와 `maxv`를 명시하는 경우, $\texttt{minv}\_\mathbb{F} \le x \le \texttt{maxv}\_\mathbb{F}$를 만족하는 실수 $x$를 읽습니다. 만약 읽은 실수가 명시한 범위를 만족하지 않는다면 `_wa` 결과로 종료합니다.

Strict mode에서는 공백을 무시하지 않으며, 현재 위치가 공백임에도 불구하고 `readDouble`을 수항하는 경우 `_pe` 결과로 종료합니다.

Validator에서 `inf`의 입력을 검증하는 경우, [`readStrictDouble`](#instreamreadstrictdouble)를 사용하는 것을 권장합니다. `readDouble`을 사용할 경우,`minv`, `maxv`와 `variableName`을 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

### InStream::readDoubles

- `std::vector<double> readDoubles(int size, int indexBase = 1)`
- `std::vector<double> readDoubles(int size, double minv, double maxv, const std::string &variablesName = "", int indexBase = 1)`
- `size`: 읽을 실수의 개수.
- `minv`, `maxv`: 읽을 실수들의 범위.
- `variablesName`: 변수 이름.
- `indexBase`: 입력 오류 시 출력할 인덱스의 시작 값.

공백으로 구분된 실수 `size`개를 읽습니다. 실수와 실수 사이에 공백이 여러 개 있어도 되며, 이 경우 연속된 여러 개의 공백을 하나의 구분자로 봅니다. 커서는 `size`번째 실수의 마지막 문자 이후로 이동합니다.

실수는 C의 `%lf`로 입력받을 수 있는 형식 중 숫자가 한 개 이상 등장하는 형식만을 허용합니다. 과학적 표기법, 예를 들어 `3.1415e2` 등은 **허용됩니다**. 실수의 형식이 올바르지 않다면 `_pe` 결과로 종료합니다. 허용되는 예는 다음과 같습니다.

- `2`
- `3.14`
- `-1.8e6`
- `.005`

`minv`와 `maxv`를 명시하는 경우, $\texttt{minv}\_\mathbb{F} \le x \le \texttt{maxv}\_\mathbb{F}$를 만족하는 실수 $x$를 `size`개 읽습니다. 만약 읽은 실수들 중 하나라도 명시한 범위를 만족하지 않는다면 `_wa` 결과로 종료합니다.

Strict mode에서는 공백을 무시하지 않으며, 토큰과 토큰 사이에는 무조건 한 개의 띄어쓰기(` ` = 32)가 있어야 하고, 띄어쓰기 이외의 공백 문자는 허용되지 않습니다. 현재 위치가 공백 문자임에도 불구하고 `readDoubles`을 수행하는 경우, 또는 토큰이 띄어쓰기 한 개보다 많은 수의 공백 문자로 구분된 경우 `_pe` 결과로 종료합니다.

Validator에서 `inf`의 입력을 검증하는 경우, [`readStrictDoubles`](#instreamreadstrictdoubles)를 사용하는 것을 권장합니다. `readDoubles`를 사용할 경우, `minv`, `maxv`와 `variableName`을 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

### InStream::readReal

- `double readReal()`
- `double readReal(double minv, double maxv, const std::string &variableName = "")`

= [readDouble](#instreamreaddouble).

### InStream::readReals

- `std::vector<double> readReals(int size, int indexBase = 1)`
- `std::vector<double> readReals(int size, double minv, double maxv, const std::string &variablesName = "", int indexBase = 1)`

= [readDoubles](#instreamreaddoubles).

### InStream::readStrictDouble

- `double readStrictDouble(double minv, double maxv, int minAfterPointDigitCount, int maxAfterPointDigitCount, const std::string &variableName = "")`
- `minv`, `maxv`: 읽을 실수의 범위.
- `minAfterPointDigitCount`, `maxAfterPointDigitCount`: 읽을 실수의 소수점 이하 자릿수의 범위.
- `variableName`: 변수 이름.

공백 문자로 구분된 실수를 읽습니다. 공백은 무시합니다. 커서는 실수의 마지막 문자 이후로 이동합니다.

실수는 `[0-9]*(\.[0-9]+)?` 형태여야 하며, 소숫점 이하에 $\left[\texttt{minAfterPointDigitCount}, \texttt{maxAfterPointDigitCount}\right]$개의 숫자를 가져야 합니다. 과학적 표기법, 예를 들어 `3.1415e2` 등은 **허용되지 않으며**, 첫 글자가 소수점인 경우도 허용되지 않습니다. 실수의 형식이 올바르지 않다면 `_pe` 결과로 종료합니다. [readDouble](#instreamreaddouble)과 입력 형식이 다름에 주의하며, 입력 함수는 소수점 이하 숫자의 수에 제한을 걸 뿐 소수점 이하 숫자의 수가 일정 이상 많아져도 `double`의 정밀도를 넘어서는 경우의 정밀도를 보장하지는 않음에 주의합니다.

`minv`와 `maxv`를 명시하는 경우, $\texttt{minv}\_\mathbb{F} \le x \le \texttt{maxv}\_\mathbb{F}$를 만족하는 실수 $x$를 읽습니다. 만약 읽은 실수가 명시한 범위를 만족하지 않는다면 `_wa` 결과로 종료합니다.

Strict mode에서는 공백을 무시하지 않으며, 현재 위치가 공백임에도 불구하고 `readStrictDouble`을 수항하는 경우 `_pe` 결과로 종료합니다.

Validator에서 `inf`의 입력을 검증하는 경우, `variableName`을 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

### InStream::readStrictDoubles

- `std::vector<double> readStrictDoubles(int size, double minv, double maxv, int minAfterPointDigitCount, int maxAfterPointDigitCount, const std::string &variablesName = "", int indexBase = 1)`
- `size`: 읽을 실수의 개수.
- `minv`, `maxv`: 읽을 실수들의 범위.
- `minAfterPointDigitCount`, `maxAfterPointDigitCount`: 읽을 실수의 소수점 이하 자릿수의 범위.
- `variablesName`: 변수 이름.
- `indexBase`: 입력 오류 시 출력할 인덱스의 시작 값.

공백으로 구분된 실수 `size`개를 읽습니다. 실수와 실수 사이에 공백이 여러 개 있어도 되며, 이 경우 연속된 여러 개의 공백을 하나의 구분자로 봅니다. 커서는 `size`번째 실수의 마지막 문자 이후로 이동합니다.

실수는 `[0-9]*(\.[0-9]+)?` 형태여야 하며, 소숫점 이하에 $\left[\texttt{minAfterPointDigitCount}, \texttt{maxAfterPointDigitCount}\right]$개의 숫자를 가져야 합니다. 과학적 표기법, 예를 들어 `3.1415e2` 등은 **허용되지 않으며**, 첫 글자가 소수점인 경우도 허용되지 않습니다. 실수의 형식이 올바르지 않다면 `_pe` 결과로 종료합니다. [readDoubles](#instreamreaddoubles)과 입력 형식이 다름에 주의하며, 입력 함수는 소수점 이하 숫자의 수에 제한을 걸 뿐 소수점 이하 숫자의 수가 일정 이상 많아져도 `double`의 정밀도를 넘어서는 경우의 정밀도를 보장하지는 않음에 주의합니다.

`minv`와 `maxv`를 명시하는 경우, $\texttt{minv}\_\mathbb{F} \le x \le \texttt{maxv}\_\mathbb{F}$를 만족하는 실수 $x$를 `size`개 읽습니다. 만약 읽은 실수들 중 하나라도 명시한 범위를 만족하지 않는다면 `_wa` 결과로 종료합니다.

Strict mode에서는 공백을 무시하지 않으며, 토큰과 토큰 사이에는 무조건 한 개의 띄어쓰기(` ` = 32)가 있어야 하고, 띄어쓰기 이외의 공백 문자는 허용되지 않습니다. 현재 위치가 공백 문자임에도 불구하고 `readStrictDoubles`을 수행하는 경우, 또는 토큰이 띄어쓰기 한 개보다 많은 수의 공백 문자로 구분된 경우 `_pe` 결과로 종료합니다.

Validator에서 `inf`의 입력을 검증하는 경우, `variableName`을 명시하여야 합니다. 명시하지 않은 경우 Polygon에서 패키지 경고가 발생합니다.

### InStream::readStrictReal

- `double readStrictReal(double minv, double maxv, int minAfterPointDigitCount, int maxAfterPointDigitCount, const std::string &variableName = "")`

= [readStrictDouble](#instreamreadstrictdouble).

### InStream::readStrictReals

- `std::vector<double> readStrictReals(int size, double minv, double maxv, int minAfterPointDigitCount, int maxAfterPointDigitCount, const std::string &variablesName = "", int indexBase = 1)`

= [readStrictDoubles](#instreamreadstrictdoubles).
