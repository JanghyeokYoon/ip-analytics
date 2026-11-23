# ch14 — 포트폴리오 매트릭스와 출원 예산, 경쟁사 감시 경보와 FTO 1차 등급

자료 기준일 2025-06-30. 이 데이터셋의 모든 기업·기관·특허·번호·인물·텍스트는 교육용으로 만든 가상의 것이며 실제 특허가 아니다.

이 폴더의 파일만으로 14장의 프롬프트와 소과제를 할 수 있다. 다른 폴더의 파일은 필요 없다.

- prompts.md: 14장의 프롬프트 3개. 준비, 먼저 보낼 말, 복사해 보낼 프롬프트를 차례로 적었다. 준비와 프롬프트는 책에 실린 글과 같다. 모두 대화형 AI에 이 폴더의 파일을 업로드해 따라 할 수 있고, 외부 서비스가 필요하거나 알아 둘 점이 있는 프롬프트에는 "진행 방법"을 더했다.
- data_dictionary.csv: 이 폴더 파일의 열 정의(file, column, type, description_ko, example).

## 파일 표

| 파일 | 행 수 | 열 수 | 구분 |
|---|---:|---:|---|
| product_spec.md | - | - | 이 장의 입력 파일 |
| topic_assignment.csv | 5,198 | 2 | 이 장의 입력 파일 |
| topic_scores.csv | 12 | 8 | 이 장의 입력 파일 |
| new_assignments.csv | 4,221 | 9 | 이 장의 입력 파일 |
| new_citations.csv | 41,119 | 8 | 이 장의 입력 파일 |
| updates.csv | 4,149 | 8 | 이 장의 입력 파일 |
| families.csv | 5,259 | 31 | 정제본 |
| applicants.csv | 5,778 | 4 | 정제본 |
| documents.csv | 41,608 | 11 | 정제본 |
| citations.csv | 77,841 | 6 | 정제본 |
| family_publications.csv | 27,780 | 9 | 정제본 |
| texts_us_part1.jsonl | 1,722 | 8 | 정제본 |
| texts_us_part2.jsonl | 1,722 | 8 | 정제본 |
| texts_us_part3.jsonl | 1,722 | 8 | 정제본 |
| texts_us_part4.jsonl | 1,722 | 8 | 정제본 |
| texts_us_part5.jsonl | 1,718 | 8 | 정제본 |

## 소과제 1

| 파일 | 행 | 내용 |
|---|---:|---|
| topic_scores.csv | 12 | 주제별 참고 점수: n_families, cagr_10y(최근 10년 성장률), novelty_score(텍스트 신규성), gap_score(공백), opportunity_score(세 점수 평균). 12장 소과제 1의 결과물이 있으면 그것을 대신 쓴다 |
| topic_assignment.csv | — | 패밀리별 주제 배정(참고판) |

정제본(이 폴더의 families.csv)의 sub_tech·assignee_std·norm_cit·legal_status와 citations.csv를 함께 쓴다. 자사는 솔베나르소재(SOLVENAR MATERIALS CO., LTD., assignee_id A0001)다.

안내
- 매력도(대표 주제의 성장률·신규성)와 상대 포지션의 분모(세부 기술별 1위 출원인 패밀리 수)는 in_analysis_set = 1(2014~2023)로 계산한다.
- 자사 강도(상대 포지션의 분자, 자사 정규화 인용 중앙값)와 정리 후보(정리 영역의 등록 특허 가운데 다른 기업의 인용이 없는 것)를 집계할 때는 자사 보유 등록 특허 전부, 곧 in_analysis_set = 0인 과거 등록 특허 60건(등록 2009~2016, legal_status가 in_force·grace인 것)도 함께 넣는다. 두 방식(C만 / C + L)의 값을 나란히 적고 어느 쪽을 썼는지 사전 결정 규칙에 밝힌다.
- 자사 패밀리가 2건 이하인 세부 기술(T3 2건, T6 1건)은 매트릭스 영역이 비거나 불안정하다. 비어 있는 영역은 강도 0으로 두고 해석에 그 사실을 적는다. 영역 유지 비율 표에서도 그 세부 기술은 "자료 부족"으로 표시한다.

## 소과제 2

| 파일 | 행 | 내용 |
|---|---:|---|
| updates.csv | 4,149 | 12개 분기(2022Q3~2025Q2)의 신규 공개·등록 문서. 열: release_quarter, doc_id, pub_number, family_id, assignee_raw, cpc, title, kind |
| new_citations.csv | 41,119 | 분기별 신규 인용(정제본 패밀리를 인용한 것). 열: release_quarter, citing_doc_id, citing_family_id, citing_assignee_raw, cited_doc_id, cited_family_id, citing_date, citing_role |
| new_assignments.csv | 4,221 | 분기별 양도 기록(발명자→고용주 양도 포함; convey_type으로 거른다) |
| product_spec.md | — | 제품 사양서(4장 폴더와 같은 파일) |

안내
- 출원 급증은 updates.csv의 공개 문서(kind = A1)를 출원인별로 분기마다 집계한다. assignee_raw는 원표기이므로 7장의 사전이나 이 폴더의 documents.csv·applicants.csv로 정규화한다. new_citations.csv의 citing_assignee_raw는 인용한 쪽 패밀리가 정제본 밖일 수 있으므로 documents.csv의 assignee_raw와 applicants.csv로 원표기–표준명 대응표를 만들어 정규화한다.
- 자사 특허 인용은 new_citations.csv에서 cited_family_id가 자사 패밀리(applicants.csv의 assignee_id = A0001)인 행을 인용하는 쪽 출원인별로 집계한다.
- 양도는 new_assignments.csv에서 convey_type이 assignment·merger이고 assignor가 경쟁사인 기록을 집계한다.
- 앞 8개 분기가 기준 기간, 뒤 4개 분기가 판정 대상이다.

## 정제본에서 가져온 파일

아래 파일은 7장의 데이터 사전(책의 표 7.1)대로 만든 정제본이다. 같은 이름의 파일은 어느 폴더에서나 내용이 같다.

| 파일 | 내용 |
|---|---|
| families.csv | 패밀리 마스터. 한 행이 패밀리 한 건(정제본 5,199건 + 자사 과거 등록 특허 60건). 대표 문서, 날짜, 출원인, CPC, sub_tech, 인용 수, norm_cit(참고판), legal_status, 심사 이력, 발명자, 만료일, in_analysis_set |
| applicants.csv | 패밀리×출원인. 공동 출원인을 모두 담은 긴 형식 |
| documents.csv | 미국 문서. set 열로 집합을 구분한다(C 정제본, U 검색 대상, B 정제본이 인용한 외부 문서, F 정제본을 인용한 외부 문서, L 자사 과거 등록 특허). B·F는 서지만 있다 |
| citations.csv | 인용 쌍(문서 단위). 정제본이 인용한 것과 정제본을 인용한 것 전부. 패밀리 단위로 집계하려면 (citing_family_id, cited_family_id)로 중복을 제거한다 |
| family_publications.csv | 패밀리×관청 문헌. US·EP·JP·CN·KR·WO 문헌의 번호·날짜·출원번호 |
| texts_us_part1.jsonl ~ texts_us_part5.jsonl | 정제본과 자사 과거 등록 특허의 미국 문서 텍스트(제목·요약·청구항 전체·청구항 1). 다섯 조각을 이어 붙이면 한 파일이다. 공개(A1)와 등록(B1/B2) 문서의 청구항이 다를 수 있다 |

families.csv를 쓸 때의 주의

- in_analysis_set이 1인 5,199건이 분석 대상인 정제본(미국 우선 연도 2014~2023년)이다. 0인 60건은 자사(솔베나르소재, SOLVENAR MATERIALS CO., LTD., assignee_id A0001)의 과거 미국 등록 특허(우선 2006~2013년)이며, 연도별 비교·점유율·네트워크에는 넣지 않는다.
- norm_cit는 참고판 값이다. 등록 후 5년 인용 창이 기준일 안에 끝난 등록 패밀리만 값이 있고, 분모는 같은 우선 연도·같은 세부 기술의 평균이다.
- legal_status(pending, abandoned, in_force, grace, lapsed, expired)는 반드시 status_as_of(2025-06-30)와 함께 읽는다.
- 미국 문서가 없는 패밀리 1건(비공개 요청, 심사 중)은 rep_pub_number가 비어 있다.
