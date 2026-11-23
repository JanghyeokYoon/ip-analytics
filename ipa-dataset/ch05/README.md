# ch05 — 프롬프트 네 요소와 교차 확인, AI 분류의 표본 검증

자료 기준일 2025-06-30. 이 데이터셋의 모든 기업·기관·특허·번호·인물·텍스트는 교육용으로 만든 가상의 것이며 실제 특허가 아니다.

이 폴더의 파일만으로 5장의 프롬프트와 소과제를 할 수 있다. 다른 폴더의 파일은 필요 없다.

- prompts.md: 5장의 프롬프트 3개. 준비, 먼저 보낼 말, 복사해 보낼 프롬프트를 차례로 적었다. 준비와 프롬프트는 책에 실린 글과 같다. 모두 대화형 AI에 이 폴더의 파일을 업로드해 따라 할 수 있고, 외부 서비스가 필요하거나 알아 둘 점이 있는 프롬프트에는 "진행 방법"을 더했다.
- data_dictionary.csv: 이 폴더 파일의 열 정의(file, column, type, description_ko, example).

## 파일 표

| 파일 | 행 수 | 열 수 | 구분 |
|---|---:|---:|---|
| classify_input.csv | 300 | 4 | 이 장의 입력 파일 |
| docs_us.csv | 9,000 | 7 | 이 장의 입력 파일 |
| eval_sample.csv | 100 | 3 | 이 장의 입력 파일 |
| subtech_definitions.csv | 8 | 6 | 이 장의 입력 파일 |
| subtech_examples.csv | 24 | 4 | 이 장의 입력 파일 |

## 소과제 1

| 파일 | 행 | 내용 |
|---|---:|---|
| docs_us.csv | 9,000 | 담당자가 이전 검색식(교재 표 6.4 ②)으로 받은 미국 특허 문서 파일. 한 행이 문서 한 건(같은 발명의 공개·등록 문서가 따로 있음). 열: doc_id, pub_number, family_id, priority_date, pub_date, assignee_raw, cpc |

- assignee_raw는 원표기 그대로여서 같은 회사가 여러 표기로 나뉘어 있다. 문서 단위·패밀리 단위·표기 통합 뒤의 세 순위를 비교한다.
- 우선 연도는 priority_date에서 얻는다. 이웃 분야 문서가 섞여 있으므로 필터 조건을 적어 둔다.

## 소과제 2

| 파일 | 행 | 내용 |
|---|---:|---|
| subtech_definitions.csv | 8 | 세부 기술 T1~T7과 X의 정의·포함·제외·예시 패밀리 3건(예시의 제목·초록은 subtech_examples.csv) |
| subtech_examples.csv | 24 | 정의에 적힌 예시 패밀리 24건의 제목·초록(code, family_id, title, abstract). 분류 대상 300건과 겹치지 않는다 |
| classify_input.csv | 300 | 분류할 초록(family_id, title, abstract, cpc). 세부 기술 열은 없다 |
| eval_sample.csv | 100 | 사람 판정. rater_a는 100건 전부, rater_b는 그 가운데 30건(나머지 빈값). 값은 T1~T7 또는 X |

주의
- classify_input.csv의 300건에는 정제본 밖(범위 밖, X) 문서와 경계 문서가 섞여 있다.
- 사람 판정에도 오류가 있을 수 있다. 두 판정자가 다른 건은 협의한 판정을 기준으로 삼는다.
- 다른 장 폴더의 families.csv에 있는 sub_tech는 정제본의 참고 분류이며, 이 소과제에서는 결과를 보기 전에 참조하지 않는다.
