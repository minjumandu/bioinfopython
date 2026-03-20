# 11장 Seaborn과 생물정보학 시각화의 확장

10장에서 우리는 Matplotlib를 이용해 기초적인 그래프를 그려 보았습니다. 선 그래프와 막대그래프만으로도 많은 정보를 확인할 수 있지만, 실제 생물정보학 데이터는 더 복잡한 구조를 가지는 경우가 많습니다. 여러 샘플 집단의 분포를 비교하거나, 많은 유전자 사이의 패턴을 한눈에 살펴보고 싶을 때는 더 풍부한 시각화 도구가 필요합니다.

이 장에서는 Seaborn을 이용해 데이터 분포와 패턴을 보다 직관적으로 시각화하는 방법을 배웁니다. Seaborn은 Matplotlib를 바탕으로 만들어진 라이브러리로, 더 세련되고 해석하기 쉬운 그래프를 간편하게 그릴 수 있게 해 줍니다. 특히 박스플롯(boxplot)과 히트맵(heatmap)은 생물정보학 데이터 분석에서 매우 자주 사용되는 대표적 시각화입니다.

![그림 11-1. Seaborn 시각화가 생물정보학 데이터의 분포와 패턴을 드러내는 방식](figures/chapter11_seaborn.svg)

그림 11-1은 이 장의 핵심을 보여 줍니다. 박스플롯은 집단별 데이터 분포를 비교하게 해 주고, 히트맵은 많은 값 사이의 패턴을 색으로 드러내어 복잡한 생물정보학 데이터를 더 쉽게 읽게 해 줍니다.

## 학습 목표

- Seaborn이 왜 유용한지 설명할 수 있다.
- 박스플롯(boxplot)의 의미를 이해하고 그릴 수 있다.
- 히트맵(heatmap)이 무엇을 보여 주는지 설명할 수 있다.
- 생물정보학 데이터의 분포와 패턴을 시각적으로 비교할 수 있다.

## 시각화가 해석에 중요한 이유

생물정보학 데이터는 종종 숫자와 표만으로는 전체적인 패턴을 파악하기 어렵습니다. 예를 들어 두 집단의 평균은 비슷해 보여도 실제 분포는 크게 다를 수 있고, 여러 유전자와 여러 샘플 사이의 관계는 숫자 목록만으로 이해하기 어렵습니다.

시각화는 이런 문제를 해결합니다. 그래프를 통해 데이터의 중심, 퍼짐, 집단 간 차이, 반복되는 패턴을 한눈에 확인할 수 있기 때문입니다. 따라서 시각화는 단순히 "예쁘게 보여 주는 단계"가 아니라, 데이터를 해석하는 핵심 과정입니다.

## 박스플롯(boxplot)으로 데이터 분포 보기

박스플롯은 데이터의 분포를 요약해서 보여 주는 그래프입니다. 중앙값, 사분위 범위, 데이터의 퍼짐 정도를 한눈에 볼 수 있어 집단 간 비교에 매우 유용합니다.

다음은 대조군과 처리군의 발현 데이터를 박스플롯으로 그리는 예입니다.

```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

data = {
    "Group": ["Control", "Control", "Control", "Treated", "Treated", "Treated"],
    "Expression": [10.2, 11.5, 9.8, 15.1, 16.3, 14.7]
}

df = pd.DataFrame(data)

sns.boxplot(data=df, x="Group", y="Expression")
plt.title("Expression Distribution by Group")
plt.show()
```

이 그래프를 보면 단순 평균값뿐 아니라, 집단별 값의 퍼짐과 중심 위치까지 함께 확인할 수 있습니다.

## 히트맵(heatmap)으로 유전자 패턴 보기

히트맵은 숫자 데이터를 색의 진하기나 색상 차이로 표현하는 그래프입니다. 값이 큰지 작은지, 어떤 행과 열에서 비슷한 패턴이 나타나는지를 빠르게 파악할 수 있습니다. 생물정보학에서는 여러 유전자와 여러 샘플의 발현 패턴을 비교할 때 자주 사용합니다.

```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

data = {
    "Sample1": [12, 8, 15],
    "Sample2": [14, 7, 18],
    "Sample3": [13, 9, 16]
}

index = ["TP53", "BRCA1", "EGFR"]
df = pd.DataFrame(data, index=index)

sns.heatmap(df, annot=True, cmap="YlGnBu")
plt.title("Gene Expression Heatmap")
plt.show()
```

이 그래프는 각 유전자의 발현값을 색으로 표현합니다. 색이 진할수록 값이 크다는 식으로 해석할 수 있으며, 여러 유전자의 패턴을 한눈에 비교할 수 있습니다.

## 시각화를 읽는 생물학적 관점

좋은 시각화는 단지 그림을 보는 데서 끝나지 않습니다. 중요한 것은 그래프를 생물학적으로 해석하는 것입니다.

예를 들어 박스플롯에서 처리군의 발현값이 대조군보다 전반적으로 높다면, 특정 처리 조건이 유전자 발현을 증가시켰다고 해석할 수 있습니다. 히트맵에서 특정 유전자들이 여러 샘플에서 함께 높게 나타난다면, 이 유전자들이 같은 생물학적 경로에 관여할 가능성을 생각해 볼 수 있습니다.

즉, 시각화는 숫자 데이터를 생물학적 질문과 연결해 주는 다리 역할을 합니다.

## 예제 실습: 유전자 데이터 분포 비교하기

이제 박스플롯과 히트맵을 함께 활용하는 작은 실습을 해 보겠습니다.

```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

# boxplot용 데이터
expr_data = {
    "Group": ["Control", "Control", "Control", "Treated", "Treated", "Treated"],
    "Expression": [10.2, 11.5, 9.8, 15.1, 16.3, 14.7]
}
expr_df = pd.DataFrame(expr_data)

sns.boxplot(data=expr_df, x="Group", y="Expression")
plt.title("Expression Distribution by Group")
plt.show()

# heatmap용 데이터
heatmap_data = {
    "Sample1": [12, 8, 15],
    "Sample2": [14, 7, 18],
    "Sample3": [13, 9, 16]
}
genes = ["TP53", "BRCA1", "EGFR"]
heatmap_df = pd.DataFrame(heatmap_data, index=genes)

sns.heatmap(heatmap_df, annot=True, cmap="coolwarm")
plt.title("Gene Expression Pattern")
plt.show()
```

이 실습은 두 가지 핵심 경험을 제공합니다.

1. 집단별 분포 차이를 boxplot으로 확인한다.
2. 여러 유전자와 샘플의 패턴을 heatmap으로 비교한다.

## 핵심 정리

- Seaborn은 Matplotlib보다 더 풍부하고 해석하기 쉬운 그래프를 쉽게 그리게 해 준다.
- 박스플롯은 데이터의 분포와 집단 간 차이를 비교하는 데 유용하다.
- 히트맵은 많은 수치 데이터의 패턴을 색으로 표현하는 데 적합하다.
- 시각화는 생물정보학 데이터 해석의 핵심 도구이다.

## 연습문제

1. Seaborn이 왜 유용한지 설명해 보자.
2. 두 집단의 발현 데이터를 박스플롯으로 그리는 코드를 작성해 보자.
3. 세 유전자와 세 샘플의 발현값으로 히트맵을 그리는 코드를 작성해 보자.
4. 박스플롯과 막대그래프의 차이를 자신의 말로 정리해 보자.
5. 히트맵이 생물정보학 데이터 분석에서 왜 자주 쓰이는지 설명해 보자.

## 이 장을 마치며

이 장에서는 Seaborn을 이용해 데이터 분포와 패턴을 보다 풍부하게 시각화하는 방법을 배웠습니다. 이제 우리는 단순한 기초 그래프를 넘어서, 복잡한 생물정보학 데이터를 더 깊이 읽고 비교할 수 있는 시각적 도구를 갖추게 되었습니다. 다음 장에서는 책 전체를 마무리하며, 생물학의 중심원리와 지금까지 익힌 파이썬 분석 방법을 하나의 흐름으로 연결해 보겠습니다.
