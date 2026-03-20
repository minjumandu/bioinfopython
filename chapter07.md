# 7장 예외 처리와 안전한 분석 프로그램

6장에서 우리는 FASTA 파일을 읽어 실제 생물정보학 데이터를 불러오는 방법을 배웠습니다. 그러나 현실의 데이터 분석은 교과서 예제처럼 항상 매끄럽게 진행되지는 않습니다. 파일 이름을 잘못 입력할 수도 있고, 비어 있는 서열을 불러올 수도 있으며, DNA 서열이라고 생각했던 데이터 안에 예상치 못한 문자가 들어 있을 수도 있습니다.

이처럼 프로그램이 실행 도중 멈추게 만드는 문제 상황을 적절히 다루는 방법이 바로 예외 처리(exception handling)입니다. 예외 처리를 배우면 단순히 "동작하는 코드"를 넘어서, 예상치 못한 상황에서도 좀 더 안전하게 작동하는 분석 프로그램을 만들 수 있습니다. 생물정보학에서는 다루는 데이터의 규모가 크고 형식이 다양하므로, 예외 처리는 매우 실용적인 주제입니다.

![그림 7-1. 예외 처리가 분석 프로그램의 안정성을 높이는 과정](figures/chapter07_exceptions.svg)

그림 7-1은 이 장의 핵심을 보여 줍니다. 오류가 발생할 수 있는 지점을 예상하고 대비하면, 프로그램은 갑자기 멈추는 대신 사용자에게 의미 있는 안내를 줄 수 있습니다.

## 학습 목표

- 예외가 무엇인지 설명할 수 있다.
- `try-except` 구문의 기본 구조를 이해할 수 있다.
- 파일이 없을 때 발생하는 오류를 처리할 수 있다.
- 잘못된 서열 입력에 대비한 안전한 분석 코드를 작성할 수 있다.

## 프로그램은 왜 멈추는가?

프로그램은 예상하지 못한 상황을 만나면 오류를 발생시키고 실행을 중단할 수 있습니다. 예를 들어 존재하지 않는 파일을 열려고 하면 오류가 발생합니다.

```python
with open("unknown.fasta", "r") as f:
    data = f.read()
```

이 코드는 `unknown.fasta`가 존재하지 않으면 실행되지 못합니다. 인간은 "파일이 없구나"라고 쉽게 이해할 수 있지만, 컴퓨터는 이를 오류 상황으로 받아들입니다.

또 다른 예도 있습니다. 만약 GC 함량을 계산하는 함수에 빈 문자열이 들어오면, 전체 길이가 0이므로 나눗셈이 불가능해질 수 있습니다. 즉, 문제는 단지 코드 문법이 틀린 데서만 생기는 것이 아니라, 입력 데이터가 예상과 다를 때도 발생합니다.

## `try-except`로 오류 다루기

파이썬에서는 `try-except` 구문을 이용해 오류가 발생할 가능성이 있는 코드를 감싸고, 문제가 생겼을 때 대신 실행할 코드를 지정할 수 있습니다.

```python
try:
    with open("unknown.fasta", "r") as f:
        data = f.read()
except FileNotFoundError:
    print("파일을 찾을 수 없습니다.")
```

이 코드의 뜻은 다음과 같습니다.

1. 먼저 `try` 블록 안의 코드를 실행한다.
2. 만약 `FileNotFoundError`가 발생하면 프로그램을 완전히 멈추지 않고,
3. 대신 `"파일을 찾을 수 없습니다."`라는 안내 문장을 출력한다.

이 방식은 사용자에게 훨씬 친절한 프로그램을 만드는 데 도움이 됩니다.

## 잘못된 입력과 없는 파일 처리하기

생물정보학 분석에서는 파일 자체가 없을 수도 있고, 파일은 있지만 내용이 비정상적일 수도 있습니다. 예를 들어 다음과 같이 DNA 서열의 유효성을 검사하는 함수와 예외 처리를 함께 사용할 수 있습니다.

```python
def validate_dna(seq):
    for base in seq:
        if base not in "ATGC":
            return False
    return True


test_seq = "ATGCX"

if validate_dna(test_seq):
    print("정상적인 DNA 서열입니다.")
else:
    print("잘못된 문자가 포함되어 있습니다.")
```

이제 파일 읽기와 결합해 보겠습니다.

```python
try:
    with open("sample.fasta", "r") as f:
        sequence = ""
        for line in f:
            if not line.startswith(">"):
                sequence += line.strip()

    if validate_dna(sequence):
        print("분석 가능한 DNA 서열입니다.")
    else:
        print("유효하지 않은 문자가 포함되어 있습니다.")

except FileNotFoundError:
    print("입력 파일이 존재하지 않습니다.")
```

이 코드는 두 가지 문제를 함께 다룹니다. 파일이 없는 경우와, 파일 안의 서열이 올바르지 않은 경우입니다.

## 자주 쓰는 코드를 모듈로 정리하기

분석 코드가 길어질수록 같은 기능을 여러 파일에서 재사용하고 싶어집니다. 이때 자주 사용하는 함수를 별도의 파이썬 파일에 모아 두면 편리합니다. 이를 모듈(module)이라고 합니다.

예를 들어 `bio_utils.py`라는 파일에 다음과 같은 함수를 저장할 수 있습니다.

```python
def validate_dna(seq):
    for base in seq:
        if base not in "ATGC":
            return False
    return True


def calculate_gc_content(seq):
    gc_count = seq.count("G") + seq.count("C")
    return (gc_count / len(seq)) * 100
```

그런 다음 다른 파일에서 불러와 사용할 수 있습니다.

```python
from bio_utils import validate_dna, calculate_gc_content

dna = "ATGCGTAC"

if validate_dna(dna):
    print(calculate_gc_content(dna))
```

이 방식은 코드를 체계적으로 관리하게 해 주며, 작은 실습을 실제 프로젝트 수준으로 확장하는 첫걸음이 됩니다.

## 예제 실습: 멈추지 않는 DNA 분석기 만들기

이제 지금까지 배운 내용을 바탕으로 보다 안전한 DNA 분석기를 만들어 보겠습니다.

```python
def parse_fasta(file_name):
    sequence = ""

    with open(file_name, "r") as f:
        for line in f:
            if line.startswith(">"):
                continue
            sequence += line.strip().upper()

    return sequence


def validate_dna(seq):
    for base in seq:
        if base not in "ATGC":
            return False
    return True


def calculate_gc_content(seq):
    gc_count = seq.count("G") + seq.count("C")
    return (gc_count / len(seq)) * 100


try:
    dna = parse_fasta("sample.fasta")

    if len(dna) == 0:
        print("서열 데이터가 비어 있습니다.")
    elif not validate_dna(dna):
        print("유효하지 않은 문자가 포함된 서열입니다.")
    else:
        print("서열 길이:", len(dna))
        print(f"GC 함량: {calculate_gc_content(dna):.2f}%")

except FileNotFoundError:
    print("FASTA 파일을 찾을 수 없습니다.")
```

이 프로그램은 다음과 같은 점에서 이전보다 더 안전합니다.

1. 파일이 없는 경우를 처리한다.
2. 비어 있는 서열인지 확인한다.
3. DNA 서열이 유효한지 검사한다.
4. 문제가 없을 때만 분석 결과를 출력한다.

## 핵심 정리

- 예외 처리는 예상치 못한 오류 상황에서 프로그램이 완전히 멈추지 않도록 돕는다.
- `try-except`는 파일 오류나 입력 오류를 다루는 기본 도구이다.
- 생물정보학에서는 잘못된 파일, 비정상적 서열, 빈 데이터에 대비해야 한다.
- 자주 쓰는 함수는 모듈로 정리하면 코드 재사용성이 높아진다.

## 연습문제

1. 예외 처리가 왜 필요한지 자신의 말로 설명해 보자.
2. 존재하지 않는 파일을 열 때 오류를 처리하는 코드를 작성해 보자.
3. DNA 서열에 `A`, `T`, `G`, `C` 외의 문자가 있는지 검사하는 함수를 작성해 보자.
4. 빈 문자열이 들어왔을 때 GC 함량 계산을 바로 하지 않도록 조건문을 추가해 보자.
5. `validate_dna()`와 `calculate_gc_content()`를 하나의 별도 파일에 저장한다고 할 때, 왜 그렇게 하는 것이 좋은지 설명해 보자.

## 이 장을 마치며

이 장에서는 예외 처리와 모듈화의 기본을 배우며 더 안전한 생물정보학 분석 프로그램을 만드는 방법을 익혔습니다. 이제 우리는 단순히 결과를 출력하는 코드가 아니라, 문제가 생겨도 스스로 점검하고 안내할 수 있는 프로그램으로 한 걸음 나아갔습니다. 다음 장에서는 NumPy를 사용해 더 많은 수치 데이터를 효율적으로 다루는 방법을 살펴보겠습니다.
