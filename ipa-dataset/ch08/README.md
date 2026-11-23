# ch08 — 지표 프로파일로 공동개발 후보 정하기, 합성 점수의 결정 근거 판정

자료 기준일 2025-06-30. 이 데이터셋의 모든 기업·기관·특허·번호·인물·텍스트는 교육용으로 만든 가상의 것이며 실제 특허가 아니다.

이 폴더의 파일만으로 8장의 프롬프트와 소과제를 할 수 있다. 다른 폴더의 파일은 필요 없다.

- prompts.md: 8장의 프롬프트 4개. 준비, 먼저 보낼 말, 복사해 보낼 프롬프트를 차례로 적었다. 준비와 프롬프트는 책에 실린 글과 같다. 모두 대화형 AI에 이 폴더의 파일을 업로드해 따라 할 수 있고, 외부 서비스가 필요하거나 알아 둘 점이 있는 프롬프트에는 "진행 방법"을 더했다.
- data_dictionary.csv: 이 폴더 파일의 열 정의(file, column, type, description_ko, example).

## 파일 표

| 파일 | 행 수 | 열 수 | 구분 |
|---|---:|---:|---|
| citing_docs.csv | 14,094 | 8 | 이 장의 입력 파일 |
| norm_cit.csv | 5,199 | 8 | 이 장의 입력 파일 |
| families.csv | 5,259 | 31 | 정제본 |
| applicants.csv | 5,778 | 4 | 정제본 |
| assignees.csv | 421 | 6 | 정제본 |
| cpc.csv | 21,349 | 4 | 정제본 |
| citations.csv | 77,841 | 6 | 정제본 |

정제본은 이 폴더의 families.csv(패밀리), applicants.csv(출원인), assignees.csv(출원인 사전), cpc.csv(분류), citations.csv(인용)를 쓴다. citing_docs.csv에는 인용한 쪽 문서의 날짜·출원인이 있다.

| 파일 | 행 | 내용 |
|---|---:|---|
| citing_docs.csv | 14,094 | citations.csv의 citing_doc_id에 나오는 모든 문서(정제본 안팎). 열: doc_id, pub_number, kind, family_id, pub_date, grant_date(등록 문서만), assignee_std, assignee_id |

지표 계산 안내
- 패밀리 단위·최초 우선일(families.priority_date) 기준. 점유율의 분모는 정제본 패밀리 전체(families.csv의 in_analysis_set = 1, 미국 우선 연도 2014~2023). in_analysis_set = 0인 자사 과거 등록 특허 60건은 비교 기간 밖이므로 제외한다.
- 세 묶음: 조성 T1·T2·T3, 계면 T5·T6, 공정 T4·T7.
- 5년 창 타인 인용: 등록 특허(families.grant_date)의 등록일부터 5년 안에 인용한 패밀리를, 인용한 쪽의 assignee_id가 다른 것만 집계한다. 창이 기준일 안에 끝난 특허만 쓴다.
- families.norm_cit는 같은 규칙으로 계산한 참고판이다. 손계산 대조에 쓴다.

정규화 인용 참고 파일
- norm_cit.csv(5,199행): 정제본 패밀리의 정규화 인용 참고 파일(family_id, assignee_std, assignee_id, priority_year, sub_tech, grant_date, norm_cit, window_complete). norm_cit는 등록 후 5년 창의 타인 전방 인용을 같은 우선 연도·세부 기술 평균으로 나눈 값이고, 창이 완성되지 않은 패밀리는 빈값(window_complete = false)이다. 소과제 1 요구사항 3과 소과제 2의 다섯 지표는 이 파일로 중앙값과 부트스트랩 구간을 구한다. citing_docs.csv로 직접 계산해 대조해도 된다.

실습할 때 알아 둘 점

- norm_cit.csv(참고판)는 건수가 적은 집단을 합치지 않고 같은 우선 연도·같은 세부 기술의 평균으로 나눈 값이다. 프롬프트 8.4처럼 30건 미만 집단을 앞뒤 연도와 합치면 값이 조금 달라진다(순위는 거의 같다). 5년 창이 완성된 특허 수는 기준일 당일을 포함하는지에 따라 몇 건 달라진다.

## 정제본에서 가져온 파일

아래 파일은 7장의 데이터 사전(책의 표 7.1)대로 만든 정제본이다. 같은 이름의 파일은 어느 폴더에서나 내용이 같다.

| 파일 | 내용 |
|---|---|
| families.csv | 패밀리 마스터. 한 행이 패밀리 한 건(정제본 5,199건 + 자사 과거 등록 특허 60건). 대표 문서, 날짜, 출원인, CPC, sub_tech, 인용 수, norm_cit(참고판), legal_status, 심사 이력, 발명자, 만료일, in_analysis_set |
| applicants.csv | 패밀리×출원인. 공동 출원인을 모두 담은 긴 형식 |
| assignees.csv | 출원인 사전. 표준명·유형·역할·국가·모회사 |
| cpc.csv | 패밀리×CPC. 순서와 발명/추가 구분 |
| citations.csv | 인용 쌍(문서 단위). 정제본이 인용한 것과 정제본을 인용한 것 전부. 패밀리 단위로 집계하려면 (citing_family_id, cited_family_id)로 중복을 제거한다 |

families.csv를 쓸 때의 주의

- in_analysis_set이 1인 5,199건이 분석 대상인 정제본(미국 우선 연도 2014~2023년)이다. 0인 60건은 자사(솔베나르소재, SOLVENAR MATERIALS CO., LTD., assignee_id A0001)의 과거 미국 등록 특허(우선 2006~2013년)이며, 연도별 비교·점유율·네트워크에는 넣지 않는다.
- norm_cit는 참고판 값이다. 등록 후 5년 인용 창이 기준일 안에 끝난 등록 패밀리만 값이 있고, 분모는 같은 우선 연도·같은 세부 기술의 평균이다.
- legal_status(pending, abandoned, in_force, grace, lapsed, expired)는 반드시 status_as_of(2025-06-30)와 함께 읽는다.
- 미국 문서가 없는 패밀리 1건(비공개 요청, 심사 중)은 rep_pub_number가 비어 있다.
