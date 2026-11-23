# ch07 — 정제 전후 비교, 법적 상태 판정과 3년 뒤 전망

자료 기준일 2025-06-30. 이 데이터셋의 모든 기업·기관·특허·번호·인물·텍스트는 교육용으로 만든 가상의 것이며 실제 특허가 아니다.

이 폴더의 파일만으로 7장의 프롬프트와 소과제를 할 수 있다. 다른 폴더의 파일은 필요 없다.

- prompts.md: 7장의 프롬프트 3개. 준비, 먼저 보낼 말, 복사해 보낼 프롬프트를 차례로 적었다. 준비와 프롬프트는 책에 실린 글과 같다. 모두 대화형 AI에 이 폴더의 파일을 업로드해 따라 할 수 있고, 외부 서비스가 필요하거나 알아 둘 점이 있는 프롬프트에는 "진행 방법"을 더했다.
- data_dictionary.csv: 이 폴더 파일의 열 정의(file, column, type, description_ko, example).

## 파일 표

| 파일 | 행 수 | 열 수 | 구분 |
|---|---:|---:|---|
| assignments_raw.csv | 6,932 | 7 | 이 장의 입력 파일 |
| docs_raw_part1.csv | 4,081 | 15 | 이 장의 입력 파일 |
| docs_raw_part2.csv | 4,081 | 15 | 이 장의 입력 파일 |
| docs_raw_part3.csv | 4,079 | 15 | 이 장의 입력 파일 |
| family_docs_raw.csv | 25,656 | 9 | 이 장의 입력 파일 |
| file_wrapper_raw.csv | 29,669 | 4 | 이 장의 입력 파일 |
| maintenance_raw.csv | 2,236 | 5 | 이 장의 입력 파일 |
| priority_raw.csv | 19,362 | 7 | 이 장의 입력 파일 |
| families.csv | 5,259 | 31 | 정제본 |
| documents.csv | 41,608 | 11 | 정제본 |
| citations.csv | 77,841 | 6 | 정제본 |

이 폴더의 파일은 일부러 지저분하게 둔 원자료다. 같은 폴더의 정제본 파일(families.csv, documents.csv, citations.csv)과 달리 형식과 규칙을 지키지 않는다. 아래에서 docs_raw.csv는 세 조각(docs_raw_part1.csv ~ docs_raw_part3.csv)을 이어 붙인 것을 가리킨다.

| 파일 | 행 | 내용 |
|---|---:|---|
| docs_raw_part1.csv ~ docs_raw_part3.csv | 12,241 | 확정 검색식(교재 표 6.4 ⑤)의 결과 문서 파일. 한 행이 미국 문서 한 건. 열: doc_id, pub_number, kind, family_id, application_number, priority_date, filing_date, publication_date, grant_date, assignee, cpc, title, abstract, first_claim |
| maintenance_raw.csv | 2,236 | 등록 특허의 유지료 사건. 열: patent_number, entity, event_code, event_date, effective_date |
| file_wrapper_raw.csv | 29,669 | 파일 래퍼 사건. 열: application_number(숫자만), event_date, event_code, event_desc. 포기(ABN)·허여(NOA)·등록(PTAC)·거절통지(CTNF·CTFR)·계속심사(RCEX) 코드 포함 |
| assignments_raw.csv | 6,932 | 양도 기록(doc_id로 결합). 출원인이 빈 공개 출원은 여기서 양수인을 찾는다 |

docs_raw.csv에 섞인 것
- 같은 발명의 공개(A1)·등록(B2) 문서가 따로 있다(같은 application_number).
- 출원인 표기 변이: 같은 기업이 대소문자·구두점·법인 표기·번역명·오타·옛 사명·자회사로 나뉘어 있다.
- 날짜 형식이 섞여 있다(YYYY-MM-DD 약 85%, YYYYMMDD 약 8%, M/D/YYYY 약 7%).
- 완전 중복 행 약 2%, 출원인 결측 약 2%, 우선일 결측 약 1%, 등록일이 출원일보다 앞서는 오류 약 0.3%.
- 이웃 분야 문서(검색 노이즈)가 섞여 있다.

번호 형식
- docs_raw.csv의 application_number는 표시형(28/470,267)이고 file_wrapper_raw.csv의 application_number는 숫자만(28470267)이다.
- maintenance_raw.csv의 patent_number는 대부분 숫자만(19730741)이지만 쉼표(19,730,741)와 앞에 0이 붙은 형식(019730741)이 섞여 있다. docs_raw.csv의 doc_id(등록 문서는 등록번호 숫자)와 맞추려면 형식을 통일해야 한다.

소과제 2의 법적 상태 판정
- 만료일은 filing_date + 20년으로 두고 존속기간 조정·포기는 없는 것으로 본다.
- 유지료 납부 기간은 등록일 + 3년·7년·11년부터 6개월, 가산금 유예 기간은 그 뒤 6개월(등록일 + 4년·8년·12년까지)이다. 기준일은 2025-06-30.
- 파일 래퍼 사건의 ABN은 포기, PTAC는 등록이다.

그 밖의 파일과 열
- docs_raw.csv의 earliest_us_filing 열: 만료일 = 이 날짜 + 20년(소과제 2 요구사항 2). 정제본 밖의 패밀리는 문서 파일의 출원일 최솟값이다.
- family_docs_raw.csv(25,656행): 문서 파일에 있는 패밀리의 관청별 문헌 목록(family_id, office, pub_number, pub_id, kind, pub_date, appl_number, appl_id, appl_date). 정제본 밖의 패밀리는 없을 수 있다.
- priority_raw.csv(19,362행): 같은 패밀리의 우선권 주장 목록(office, appl_id, prior_seq, prior_office, prior_number, prior_date, prior_kind). priority_date는 이 목록의 최솟값으로 채우고, 목록이 없는 패밀리는 문서 파일의 최솟값으로 한다.
- 소과제 1은 트랙 A(패밀리 통합·노이즈 제거)와 트랙 B(출원인 정규화·정제 전후 비교) 가운데 하나를 택할 수 있다. 소과제 2는 소과제 1을 하지 않았다면 이 폴더의 families.csv(in_analysis_set = 1)를 정제본으로 쓴다.

실습할 때 알아 둘 점

- family_docs_raw.csv의 한국 문헌 가운데 출원공개 전에 등록공고가 된 출원은 등록공보(B1) 행만 있다.
- assignee가 빈 문서 241건 가운데 assignments_raw.csv에 doc_id가 같은 양도 기록이 있는 것은 100건이다. 나머지는 같은 family_id의 다른 문서에 출원인이 적혀 있거나 그 문서의 양도 기록에 양수인이 있다. 끝까지 채우지 못한 건수를 정제 로그에 적는다.

## 정제본에서 가져온 파일

아래 파일은 7장의 데이터 사전(책의 표 7.1)대로 만든 정제본이다. 같은 이름의 파일은 어느 폴더에서나 내용이 같다.

| 파일 | 내용 |
|---|---|
| families.csv | 패밀리 마스터. 한 행이 패밀리 한 건(정제본 5,199건 + 자사 과거 등록 특허 60건). 대표 문서, 날짜, 출원인, CPC, sub_tech, 인용 수, norm_cit(참고판), legal_status, 심사 이력, 발명자, 만료일, in_analysis_set |
| documents.csv | 미국 문서. set 열로 집합을 구분한다(C 정제본, U 검색 대상, B 정제본이 인용한 외부 문서, F 정제본을 인용한 외부 문서, L 자사 과거 등록 특허). B·F는 서지만 있다 |
| citations.csv | 인용 쌍(문서 단위). 정제본이 인용한 것과 정제본을 인용한 것 전부. 패밀리 단위로 집계하려면 (citing_family_id, cited_family_id)로 중복을 제거한다 |

families.csv를 쓸 때의 주의

- in_analysis_set이 1인 5,199건이 분석 대상인 정제본(미국 우선 연도 2014~2023년)이다. 0인 60건은 자사(솔베나르소재, SOLVENAR MATERIALS CO., LTD., assignee_id A0001)의 과거 미국 등록 특허(우선 2006~2013년)이며, 연도별 비교·점유율·네트워크에는 넣지 않는다.
- norm_cit는 참고판 값이다. 등록 후 5년 인용 창이 기준일 안에 끝난 등록 패밀리만 값이 있고, 분모는 같은 우선 연도·같은 세부 기술의 평균이다.
- legal_status(pending, abandoned, in_force, grace, lapsed, expired)는 반드시 status_as_of(2025-06-30)와 함께 읽는다.
- 미국 문서가 없는 패밀리 1건(비공개 요청, 심사 중)은 rep_pub_number가 비어 있다.
