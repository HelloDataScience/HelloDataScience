## 안녕하세요, 헬로데이터사이언스입니다 👋

데이터 사이언스 강사 **나성호**입니다. 2019년부터 헬로데이터사이언스를 운영하며 기업과 대학에서 Python·R을 활용한 데이터 분석과 머신러닝을 강의하고 있습니다. 요즘은 코드를 모르는 분도 생성형 AI의 도움을 받아 데이터 분석을 시작할 수 있는 방법을 함께 다루고 있습니다.

전업 강사가 되기 전에는 2002년부터 약 17년간 금융회사에서 마케터 겸 데이터 분석가로 고객 데이터를 다뤘습니다. 그래서 "이 기법이 이론적으로 무엇인가"보다 "현업에서 어디에 어떻게 쓰는가"에 무게를 두고 강의합니다.

### 📚 강의 자료

| 저장소 | 내용 |
|:---|:---|
| [python-data-prep](https://github.com/HelloDataScience/python-data-prep) | 파이썬 데이터 수집과 전처리: 웹 스크레이핑, Open API, Selenium, 정형·텍스트 데이터 전처리 |
| [stock-data-analysis](https://github.com/HelloDataScience/stock-data-analysis) | 파이썬을 활용한 주식 데이터 분석: 주가 수집과 전처리, 수익률·변동성 계산과 시각화 |
| [python-using-gpt](https://github.com/HelloDataScience/python-using-gpt) | Python using GPT 강의 실습 노트북과 데이터 |
| [korea-tech](https://github.com/HelloDataScience/korea-tech) | 한국기술교육대학교 R 데이터 분석 강의 자료 |
| [datasets](https://github.com/HelloDataScience/datasets) | R·Python 강의에서 사용하는 실습 데이터 |

### 🛠️ 파이썬 패키지: hds

[![PyPI version](https://img.shields.io/pypi/v/hds)](https://pypi.org/project/hds/)
[![Python](https://img.shields.io/pypi/pyversions/hds)](https://pypi.org/project/hds/)

[**hds**](https://github.com/HelloDataScience/hds)는 강의에서 반복해서 그리는 그래프와 통계 분석 작업을 함수 하나로 끝낼 수 있도록 모아 만든 파이썬 패키지입니다. 그래프 코드를 짜느라 시간을 쓰기보다 결과를 읽고 판단하는 데 집중하자는 생각으로 만들었습니다.

- 📊 `plot`: 범주별 상자그림, 산점도와 회귀직선, 상관계수 히트맵, 의사결정나무, 변수 중요도, ROC·PR 곡선, 주성분·군집 진단 그래프
- 📈 `stat`: 변수선택법, 잔차 진단, VIF(Variance Inflation Factor, 분산팽창계수), 표준화 회귀계수, 회귀·분류 성능 지표, A/B 테스트 표본 크기와 검정력 계산
- 🔤 한글 폰트: `plot.set_font()`에 구글 폰트명을 넣으면 폰트를 따로 설치하지 않아도 그래프에 한글이 바로 나옵니다.

**설치**

```bash
pip install hds
```

**사용 예**

```python
import seaborn as sns
from hds import plot

plot.set_font(font_name='Gowun Batang')
iris = sns.load_dataset('iris')
plot.box_group(data=iris, x='species', y='petal_length')
```

함수 목록과 자세한 사용법은 [hds 저장소](https://github.com/HelloDataScience/hds)에 정리해 두었습니다.

### 📖 저서

- [**Must Have 나성호의 R 데이터 분석 입문**](https://www.hds.ai.kr/books/) (골든래빗, 2021) — 예제 소스코드는 [DAwR](https://github.com/HelloDataScience/DAwR) 저장소에 있습니다.

### 📫 연락처

- 블로그: [Hello Data Science](https://www.hds.ai.kr) — 강의실에서 다루는 내용을 글로 정리하고 있습니다.
- 이메일: hellodatasciencekorea@gmail.com
