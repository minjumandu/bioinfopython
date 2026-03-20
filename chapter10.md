# 10장 데이터 정제와 기초 시각화

9장에서 우리는 Pandas 데이터프레임(DataFrame)을 이용해 유전자 데이터를 정리하는 방법을 배웠습니다. 하지만 실제 데이터는 늘 깔끔하지 않습니다. 값이 비어 있거나, 예상보다 지나치게 크거나 작은 값이 들어 있거나, 같은 그룹끼리 비교해야 할 정보가 섞여 있을 수도 있습니다. 따라서 분석의 다음 단계는 데이터를 정제하고, 그 결과를 시각적으로 이해하는 것입니다.

시각화는 숫자를 단순히 나열하는 것을 넘어, 데이터의 패턴과 차이를 직관적으로 보여 줍니다. 생물정보학에서는 발현 변화, 샘플 간 비교, 염기 비율, 집단별 차이 등을 그래프로 표현하는 일이 매우 중요합니다. 이 장에서는 Pandas와 Matplotlib를 이용해 데이터를 정리하고 간단한 그래프를 그리는 방법을 배웁니다.

![그림 10-1. 데이터 정제와 시각화가 생물정보학 해석으로 이어지는 흐름](figures/chapter10_visualization.svg)

그림 10-1은 이 장의 핵심을 보여 줍니다. 먼저 데이터를 정리하고, 그다음 그래프로 표현하면 표만 봤을 때는 보이지 않던 구조가 더 분명하게 드러납니다.

## 학습 목표

- 데이터 정제가 왜 필요한지 설명할 수 있다.
- 결측치를 확인하고 간단히 처리할 수 있다.
- 그룹별 통계값을 계산할 수 있다.
- Matplotlib로 선 그래프와 막대그래프를 그릴 수 있다.

## 결측치와 이상치 다루기

데이터 정제의 가장 기본적인 대상은 결측치와 이상치입니다. 결측치란 값이 비어 있는 경우를 말하고, 이상치는 다른 값과 비교했을 때 지나치게 크거나 작은 값을 말합니다.

예를 들어 다음과 같은 데이터프레임을 생각해 볼 수 있습니다.

```python
import pandas as pd

data = {
    "Gene": ["TP53", "BRCA1", "EGFR", "MYC"],
    "Expression": [12.5, None, 15.2, 100.0]
}

df = pd.DataFrame(data)
print(df)
```

여기서 `None`은 결측치이고, `100.0`은 다른 값들과 비교할 때 매우 큰 값이므로 이상치처럼 보일 수 있습니다.

결측치는 다음과 같이 확인할 수 있습니다.

```python
print(df.isna())
print(df.isna().sum())
```

결측치를 제거하려면 `dropna()`를 사용할 수 있습니다.

```python
clean_df = df.dropna()
print(clean_df)
```

## 그룹별 통계 계산하기

생물정보학에서는 샘플을 여러 그룹으로 나누어 비교하는 경우가 많습니다. 예를 들어 대조군과 처리군, 정상 조직과 암 조직, 서로 다른 종이나 조건별 집단을 비교할 수 있습니다.

```python
import pandas as pd

data = {
    "Group": ["Control", "Control", "Treated", "Treated"],
    "Expression": [10.2, 11.5, 15.1, 16.3]
}

df = pd.DataFrame(data)

group_mean = df.groupby("Group")["Expression"].mean()
print(group_mean)
```

이 코드는 그룹별 평균 발현값을 계산합니다. 이런 작업은 데이터의 전체 흐름을 빠르게 파악하는 데 매우 유용합니다.

## Matplotlib로 선 그래프 그리기

시각화의 가장 기본적인 도구 중 하나는 선 그래프입니다. 예를 들어 시간에 따라 발현량이 어떻게 바뀌는지 보고 싶을 때 사용할 수 있습니다.

```python
import matplotlib.pyplot as plt

time_points = [1, 2, 3, 4, 5]
expression = [5, 8, 12, 10, 14]

plt.plot(time_points, expression, marker="o")
plt.title("Gene Expression Over Time")
plt.xlabel("Time")
plt.ylabel("Expression")
plt.show()
```

이 그래프는 시간에 따른 발현량의 변화를 시각적으로 보여 줍니다.

## DNA 염기 비율을 막대그래프로 표현하기

서열 데이터에서도 시각화는 유용합니다. 예를 들어 DNA 서열에서 A, T, G, C의 개수를 세고 막대그래프로 그릴 수 있습니다.

```python
import matplotlib.pyplot as plt

dna = "ATGCGATTCGGC"
counts = {
    "A": dna.count("A"),
    "T": dna.count("T"),
    "G": dna.count("G"),
    "C": dna.count("C")
}

plt.bar(counts.keys(), counts.values())
plt.title("DNA Base Composition")
plt.xlabel("Base")
plt.ylabel("Count")
plt.show()
```

이 그래프는 서열의 염기 구성을 한눈에 파악하게 해 줍니다. 숫자를 그냥 읽을 때보다 훨씬 직관적입니다.

## 예제 실습: 발현 변화와 염기 구성 시각화

이제 두 가지 시각화를 함께 연습해 보겠습니다.

```python
import pandas as pd
import matplotlib.pyplot as plt

data = {
    "Group": ["Control", "Control", "Treated", "Treated"],
    "Expression": [10.2, 11.5, 15.1, 16.3]
}

df = pd.DataFrame(data)
group_mean = df.groupby("Group")["Expression"].mean()

plt.bar(group_mean.index, group_mean.values)
plt.title("Average Expression by Group")
plt.xlabel("Group")
plt.ylabel("Average Expression")
plt.show()

dna = "ATGCGATTCGGC"
base_counts = [dna.count("A"), dna.count("T"), dna.count("G"), dna.count("C")]

plt.bar(["A", "T", "G", "C"], base_counts)
plt.title("DNA Base Composition")
plt.xlabel("Base")
plt.ylabel("Count")
plt.show()
```

이 예제는 그룹별 평균 발현량과 DNA 염기 조성을 각각 시각화합니다. 즉, 표 데이터와 서열 데이터를 모두 그래프로 표현하는 기본 경험을 제공합니다.

## 핵심 정리

- 데이터 정제는 분석 전에 꼭 필요한 과정이다.
- 결측치는 `isna()`와 `dropna()`로 확인하고 처리할 수 있다.
- `groupby()`를 사용하면 그룹별 통계량을 계산할 수 있다.
- Matplotlib는 선 그래프와 막대그래프 같은 기본 시각화를 지원한다.
- 시각화는 데이터의 패턴을 더 직관적으로 이해하게 해 준다.

## 연습문제

1. 결측치와 이상치가 무엇인지 설명해 보자.
2. 결측치가 포함된 간단한 데이터프레임을 만들고 `isna()`로 확인해 보자.
3. `dropna()`를 사용해 결측치를 제거해 보자.
4. 그룹별 평균 발현값을 계산하는 코드를 작성해 보자.
5. DNA 서열의 염기 개수를 막대그래프로 나타내는 코드를 작성해 보자.

## 이 장을 마치며

이 장에서는 데이터를 정제하고, 그 결과를 기초적인 그래프로 표현하는 방법을 배웠습니다. 이제 우리는 숫자와 표를 정리하는 것을 넘어, 생물정보학 데이터를 시각적으로 읽는 첫걸음을 내디뎠습니다. 다음 장에서는 Seaborn을 이용해 더 세련되고 풍부한 시각화를 수행하며, 데이터 분포와 패턴을 더 깊이 탐색해 보겠습니다.
