---
title: "반도체 품질 데이터로, 보고서까지 자동으로 완성해봅시다"
subtitle: "9회 연재를 마무리하는 실전 종합 실습"
date: 2026-08-05
categories:
  - 생성형AI
tags:
  - 생성형AI
  - 데이터분석입문
  - 반도체품질
  - ChatGPT
excerpt: "Lot 정보, 검사 결과, 결함 상세, 계측 결과 네 가지 반도체 품질 데이터를 가지고, 두 개의 GPT로 분석 계획을 세우고 보고서 템플릿의 빈칸까지 자동으로 채우는 실습으로 9회 연재를 마무리합니다."
---

안녕하세요, 헬로데이터사이언스 나성호입니다.

드디어 9회차, 이번 연재의 마지막 회차입니다. 8회차에서 고객 이탈 데이터로 "계획 세우기(md) → 코드 통째로 받기(ipynb) → 실행하기 → 요약받기"의 전체 흐름을 익히셨죠. 오늘은 같은 흐름을, 좀 더 실전에 가까운 반도체 품질 데이터로 한 번 더 해보면서, 이번엔 결과를 보고서 템플릿에 자동으로 채워 넣는 것까지 완성해보겠습니다.

## 오늘 쓸 데이터부터 받으세요

이번 실습에서는 반도체 품질 데이터를 모사한 더미 데이터셋 4종과, 채워 넣을 보고서 템플릿 1종을 사용합니다. 전부 아래 저장소에서 내려받으실 수 있습니다.

- 저장소: [github.com/HelloDataScience/python-using-gpt](https://github.com/HelloDataScience/python-using-gpt)

| 구분 | 파일명 | 행 수 | 상세 내용 |
|---|---|---|---|
| ① Lot 정보 | [lot_info.csv](https://raw.githubusercontent.com/HelloDataScience/python-using-gpt/main/lot_info.csv) | 150 | Lot별 제품, 라인, 생산 기간 등 |
| ② 검사 결과 | [inspection.csv](https://raw.githubusercontent.com/HelloDataScience/python-using-gpt/main/inspection.csv) | 150 | Lot별 수율, 결함 밀도, 최종 판정 등 |
| ③ 결함 상세 | [defect.csv](https://raw.githubusercontent.com/HelloDataScience/python-using-gpt/main/defect.csv) | 136 | 결함 유형, 결함 건수, 심각도 등 |
| ④ 계측 결과 | [metrology.csv](https://raw.githubusercontent.com/HelloDataScience/python-using-gpt/main/metrology.csv) | 600 | 공정별 측정값, 이상 여부 등 |
| 보고서 템플릿 | [report_template.xlsx](https://raw.githubusercontent.com/HelloDataScience/python-using-gpt/main/report_template.xlsx) | - | 채워 넣을 보고서 양식 |

네 개의 데이터 파일 모두 Lot 식별자인 `Lot_ID`를 공통 키로 연결할 수 있도록 설계되어 있습니다.

하나씩 조금 더 들여다보겠습니다.

**lot_info.csv**는 Lot별 생산 정보를 담은 기준 테이블입니다. 한 행이 하나의 Lot을 의미하며, 총 150개 Lot으로 구성되어 있습니다. Product는 4종(DRAM_A, DRAM_B, Logic_A, NAND_A), Fab은 2종(FAB1, FAB2)입니다. Start_Date와 End_Date는 날짜시간형으로 변환해야 합니다.

**inspection.csv**는 Lot별 최종 검사 결과를 담은 테이블입니다. lot_info와 1:1로 대응하며, 총 150행으로 구성되어 있습니다. Final_Judgment는 3종(Pass, Hold, Review)입니다.

**defect.csv**는 결함이 발생한 Lot에 한해, 결함 유형과 규모를 기록한 테이블입니다. 전체 150개 Lot 중에서 결함이 있는 69개 Lot만 포함하고 있습니다. 하나의 Lot에 여러 유형의 결함이 있을 수 있으므로, lot_info와 1:m 관계를 가집니다. Severity는 3종(High, Medium, Low), Defect_Type은 7종(Particle, Scratch, Open defect, Pattern bridge, Overlay fail, Thickness fail, CD outlier)입니다.

**metrology.csv**는 Lot별 주요 공정 단계에서 측정한 값을 담은 테이블입니다. 150개 Lot별로 4개 공정 단계를 측정하므로, 총 600행으로 구성되어 있습니다. Process_Step은 4종(Lithography, Etch, Deposition, CMP)입니다.

## 오늘의 목표 — 보고서 템플릿 자동 완성

오늘은 단순히 분석만 하는 게 아니라, 미리 준비된 '반도체 품질 분석 보고서' 엑셀 템플릿의 빈칸을 자동으로 채우는 것까지 해보겠습니다. 템플릿에는 보고 기간, 작성일자 같은 기본 정보 외에, 이런 항목들이 비어 있습니다.

- **주요 KPI 요약**: 평균 수율(%), 불량률(%), 검사 Lot 수, 주요 불량 유형, 수율 미달 Lot 수, 최저 수율 제품
- **제품별 품질 지표**: 제품(DRAM_A, DRAM_B, Logic_A, NAND_A)별 검사 Lot 수, 평균 수율(%), 불량률(%)
- **분석 결과 요약**: 생성형 AI가 작성한 분석 결과 및 주의사항 문장

템플릿에는 "연한 노란색 셀은 생성형 AI가 생성한 코드가 자동으로 채우는 입력 영역입니다"라는 안내 문구도 함께 있습니다. 이 노란 셀들을 채우는 게 오늘의 최종 목표입니다.

## 실습 프로세스 — 8회차와 거의 똑같습니다, 첨부 파일만 늘어났을 뿐

| 단계 | 사용할 도구 | 실습 내용 | 산출물 |
|---|---|---|---|
| 1단계 | Data Analysis Planner | 데이터 4종과 보고서 템플릿을 함께 첨부, 분석 계획 요청 | 분석 계획 md 파일 |
| 2단계 | Python Code Assistant | md 계획 파일과 데이터 4종, 보고서 템플릿을 함께 첨부해 코드 작성 요청 | 분석 코드가 담긴 ipynb 파일 |
| 3단계 | Colab | ipynb 파일을 열어 셀 단위로 실행, 에러 발생 시 Python Code Assistant에 메시지를 붙여넣어 수정 | 전처리 결과, 요약 지표, 채워진 보고서 |
| 4단계 | Excel | 자동으로 채워진 보고서 템플릿의 노란 셀을 확인 | 자동화된 보고서 초안 |

8회차에서 익힌 흐름과 거의 같습니다. 다만 이번엔 Data Analysis Planner와 Python Code Assistant에 첨부하는 파일이 데이터 1개에서 데이터 4개 + 보고서 템플릿까지, 총 5개로 늘어났다는 점만 다릅니다.

**1단계**에서는 Data Analysis Planner에 `lot_info.csv`, `inspection.csv`, `defect.csv`, `metrology.csv`, `report_template.xlsx` 다섯 개 파일을 함께 첨부하고, 분석 계획을 요청합니다. 그러면 데이터 구조와 템플릿의 빈칸을 먼저 확인한 뒤, 그 빈칸을 채우는 데 필요한 분석 계획을 md 파일로 만들어줍니다.

**2단계**에서는 이 md 파일을 방금 첨부했던 데이터 파일들과 함께 Python Code Assistant에 다시 첨부합니다. 그러면 데이터를 불러오고 전처리하고 지표를 계산해서, 최종적으로 보고서 템플릿의 노란 셀까지 채워 넣는 전체 코드가 담긴 ipynb 파일을 한 번에 받게 됩니다.

**3단계**에서 에러가 발생하면 8회차와 똑같이 전체 에러 메시지를 복사해서 Python Code Assistant에 붙여넣으면 수정된 코드를 받을 수 있습니다. 그런데 한 가지 새로운 상황이 생길 수 있습니다. 보고서 템플릿에 지표가 잘못 입력된 경우인데, 이럴 때는 에러 메시지가 따로 없으니 **보고서 화면을 캡처해서 이미지로 Python Code Assistant에 첨부**하면 됩니다. 그림을 보고 뭐가 잘못됐는지 파악해서 수정된 코드를 알려줍니다.

## 참고로, 이 템플릿은 실제 업무 양식이 아닙니다

한 가지 짚고 넘어갈 부분이 있습니다. 오늘 쓰는 보고서 템플릿은 실습을 위해 만든 예시이지, 실제 업무에서 쓰는 보고서 양식을 참고하지 않았다는 점을 알아두시기 바랍니다. 실제 업무에 적용하실 때는 반드시 소속 조직의 보고서 양식과 승인된 도구 안에서 진행하셔야 합니다. 4회차에서 말씀드렸던 정보보안 유의사항, 기억하시죠?

## 9회 연재를 마치며

1회차에서 "말귀 못 알아듣던 신입"이었던 AI가 규칙 기반 AI → 머신러닝 → 딥러닝을 거쳐 생성형 AI라는 '에이스'가 되기까지의 과정을 살펴봤고, 그 에이스가 어떤 원리로 답을 만드는지(2회차), 왜 가끔 거짓말을 하고 우리를 게으르게 만드는지(3회차), 어떻게 활용해야 하는지(4회차)를 다뤘습니다. 그리고 5, 6회차에서 좋은 프롬프트를 쓰는 법을, 7, 8, 9회차에서 Python·Colab 도구를 익혀 실제 데이터를 분석하고 보고서까지 완성하는 데까지 왔습니다.

정리하자면 이렇습니다. 생성형 AI는 이제 코드를 몰라도 데이터 분석을 시작할 수 있게 해주는 강력한 에이스입니다. 하지만 이 에이스가 써 온 결과물의 결재는, 언제나 여러분의 몫입니다. 데이터 구조를 확인하고, 계산식을 검증하고, 결과가 상식에 맞는지 판단하는 건 사람만이 할 수 있는 일입니다.

9회에 걸쳐 함께해주셔서 감사합니다. 앞으로도 생성형 AI와 데이터 분석에 관한 이야기, 계속 이어가겠습니다. 감사합니다.
