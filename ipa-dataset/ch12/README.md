# ch12 — 기술기회 점수와 첫 출원 시점, 심사 모형과 거절 대응 예산

자료 기준일 2025-06-30. 이 데이터셋의 모든 기업·기관·특허·번호·인물·텍스트는 교육용으로 만든 가상의 것이며 실제 특허가 아니다.

이 폴더의 파일만으로 12장의 프롬프트와 소과제를 할 수 있다. 다른 폴더의 파일은 필요 없다.

- prompts.md: 12장의 프롬프트 4개. 준비, 먼저 보낼 말, 복사해 보낼 프롬프트를 차례로 적었다. 준비와 프롬프트는 책에 실린 글과 같다. 모두 대화형 AI에 이 폴더의 파일을 업로드해 따라 할 수 있고, 외부 서비스가 필요하거나 알아 둘 점이 있는 프롬프트에는 "진행 방법"을 더했다.
- data_dictionary.csv: 이 폴더 파일의 열 정의(file, column, type, description_ko, example).

## 파일 표

| 파일 | 행 수 | 열 수 | 구분 |
|---|---:|---:|---|
| examination.csv | 5,590 | 13 | 이 장의 입력 파일 |
| topic_rules.csv | 12 | 6 | 이 장의 입력 파일 |
| families.csv | 5,259 | 31 | 정제본 |
| applicants.csv | 5,778 | 4 | 정제본 |
| citations.csv | 77,841 | 6 | 정제본 |
| cpc.csv | 21,349 | 4 | 정제본 |
| texts_us_part1.jsonl | 1,722 | 8 | 정제본 |
| texts_us_part2.jsonl | 1,722 | 8 | 정제본 |
| texts_us_part3.jsonl | 1,722 | 8 | 정제본 |
| texts_us_part4.jsonl | 1,722 | 8 | 정제본 |
| texts_us_part5.jsonl | 1,718 | 8 | 정제본 |
| corpus_part1.jsonl | 2,799 | 7 | 11장 폴더와 같은 파일 |
| corpus_part2.jsonl | 2,799 | 7 | 11장 폴더와 같은 파일 |
| embeddings_128d.csv | 5,598 | 129 | 11장 폴더와 같은 파일 |
| embeddings_meta.json | - | - | 11장 폴더와 같은 파일 |

| 파일 | 행 | 내용 |
|---|---:|---|
| topic_rules.csv | 12 | 연구 주제 후보 12개의 키워드 규칙(topic_id, topic_name, sub_tech, include_terms, exclude_terms, priority). 제목+요약+청구항 1을 소문자로 바꾸어 include_terms가 가장 많이 나타나는 주제에 배정하고, 동점이면 priority가 작은 주제, exclude_terms가 있으면 그 주제를 뺀다 |
| examination.csv | 5,590 | 정제본 패밀리(in_analysis_set = 1)의 미국 출원 심사 기록(자사 과거 등록 특허 60건의 출원은 들어 있지 않다). 열: application_number, application_id, doc_id, family_id, examiner_id, art_unit, filing_date, outcome, disposal_date, n_nonfinal, n_final, rejection_types, has_rejection_data |

안내
- 소과제 1의 텍스트는 이 폴더의 texts_us_part1.jsonl ~ texts_us_part5.jsonl 또는 corpus_part1.jsonl·corpus_part2.jsonl을 쓴다. 임베딩은 embeddings_128d.csv(모델 정보는 embeddings_meta.json)다. 신규성과 이웃 밀도는 11장의 방법으로 계산하고 임베딩 모델·k를 기록한다.
- 소과제 2의 세부 기술은 이 폴더 families.csv의 sub_tech, 출원 연도는 examination.filing_date의 연도다. 계속·분할 출원도 한 행이므로 분모 정의를 적는다.
- rejection_types는 has_rejection_data가 true인 출원만 값이 있다. 빈값과 0을 구분한다.

실습할 때 알아 둘 점

- 소과제 1의 분기별 공개 건수는 families.csv의 publication_date(미국 최초 공개일)로 집계한다.
- examination.csv의 출원 연도는 2014년부터다. 출원인의 과거 등록률처럼 앞선 자료가 필요한 변수는 가장 이른 두 해(2014~2015년)를 떼어 계산한다.
- 상위 주제의 패밀리는 수백 건씩이다. 대화형 AI에게 초록과 청구항을 읽혀 개체를 뽑을 때는 100건 안팎을 무작위로 뽑아 쓴다(프롬프트 12.4).

## 정제본에서 가져온 파일

아래 파일은 7장의 데이터 사전(책의 표 7.1)대로 만든 정제본이다. 같은 이름의 파일은 어느 폴더에서나 내용이 같다.

| 파일 | 내용 |
|---|---|
| families.csv | 패밀리 마스터. 한 행이 패밀리 한 건(정제본 5,199건 + 자사 과거 등록 특허 60건). 대표 문서, 날짜, 출원인, CPC, sub_tech, 인용 수, norm_cit(참고판), legal_status, 심사 이력, 발명자, 만료일, in_analysis_set |
| applicants.csv | 패밀리×출원인. 공동 출원인을 모두 담은 긴 형식 |
| citations.csv | 인용 쌍(문서 단위). 정제본이 인용한 것과 정제본을 인용한 것 전부. 패밀리 단위로 집계하려면 (citing_family_id, cited_family_id)로 중복을 제거한다 |
| cpc.csv | 패밀리×CPC. 순서와 발명/추가 구분 |
| texts_us_part1.jsonl ~ texts_us_part5.jsonl | 정제본과 자사 과거 등록 특허의 미국 문서 텍스트(제목·요약·청구항 전체·청구항 1). 다섯 조각을 이어 붙이면 한 파일이다. 공개(A1)와 등록(B1/B2) 문서의 청구항이 다를 수 있다 |
| corpus_part1.jsonl, corpus_part2.jsonl | 패밀리별 텍스트(제목·요약·청구항 1). 정제본 패밀리 전체에 정제본 밖 400건을 더했다. 11장 폴더와 같은 파일 |
| embeddings_128d.csv | 고정 임베딩(128차원, L2 정규화). 11장 폴더와 같은 파일 |
| embeddings_meta.json | 고정 임베딩의 모델·차원 정보. 11장 폴더와 같은 파일 |

families.csv를 쓸 때의 주의

- in_analysis_set이 1인 5,199건이 분석 대상인 정제본(미국 우선 연도 2014~2023년)이다. 0인 60건은 자사(솔베나르소재, SOLVENAR MATERIALS CO., LTD., assignee_id A0001)의 과거 미국 등록 특허(우선 2006~2013년)이며, 연도별 비교·점유율·네트워크에는 넣지 않는다.
- norm_cit는 참고판 값이다. 등록 후 5년 인용 창이 기준일 안에 끝난 등록 패밀리만 값이 있고, 분모는 같은 우선 연도·같은 세부 기술의 평균이다.
- legal_status(pending, abandoned, in_force, grace, lapsed, expired)는 반드시 status_as_of(2025-06-30)와 함께 읽는다.
- 미국 문서가 없는 패밀리 1건(비공개 요청, 심사 중)은 rep_pub_number가 비어 있다.
