# InStream

- 해당 내용: **Validator**, **Checker**, **Interactor**

`InStream`은 입력과 출력에 대한 검증 등을 쉽게 할 수 있는 여러 유틸리티 함수를 제공합니다.

## inf, ouf, ans

- `InStream inf`: Validator, checker, interactor에서 테스트 케이스 파일.
- `InStream ouf`: Checker, interactor에서 참가자의 출력.
- `InStream ans`: Checker, interactor에서 출제자의 답안.

`inf`, `ouf`, `ans`는 `InStream`의 인스턴스로, 전역 변수입니다.

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

(TODO: InStream 메서드 설명)