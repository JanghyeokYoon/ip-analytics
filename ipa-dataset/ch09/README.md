# ch09 — 인용 네트워크로 매입 후보 정하기, 공동출원 링크 예측

자료 기준일 2025-06-30. 이 데이터셋의 모든 기업·기관·특허·번호·인물·텍스트는 교육용으로 만든 가상의 것이며 실제 특허가 아니다.

이 폴더의 파일만으로 9장의 프롬프트와 소과제를 할 수 있다. 다른 폴더의 파일은 필요 없다.

- prompts.md: 9장의 프롬프트 3개. 준비, 먼저 보낼 말, 복사해 보낼 프롬프트를 차례로 적었다. 준비와 프롬프트는 책에 실린 글과 같다. 모두 대화형 AI에 이 폴더의 파일을 업로드해 따라 할 수 있고, 외부 서비스가 필요하거나 알아 둘 점이 있는 프롬프트에는 "진행 방법"을 더했다.
- data_dictionary.csv: 이 폴더 파일의 열 정의(file, column, type, description_ko, example).

## 파일 표

| 파일 | 행 수 | 열 수 | 구분 |
|---|---:|---:|---|
| candidates.csv | 8 | 5 | 이 장의 입력 파일 |
| families.csv | 5,259 | 31 | 정제본 |
| citations.csv | 77,841 | 6 | 정제본 |
| applicants.csv | 5,778 | 4 | 정제본 |
| citing_docs.csv | 14,094 | 8 | 8장 폴더와 같은 파일 |

정제본은 이 폴더의 families.csv, citations.csv, applicants.csv를 쓴다.

| 파일 | 행 | 내용 |
|---|---:|---|
| candidates.csv | 8 | 매입 후보 8건(국립 연구소 5건, 경쟁 소재 기업 3건). 열: candidate_id, family_id, assignee_std, pub_number, title |

안내
- 인용 네트워크의 노드는 정제본 패밀리(family_id, families.csv의 in_analysis_set = 1), 엣지는 citations.csv의 (citing_family_id → cited_family_id)를 중복 제거한 것이다. 정제본 밖 문서(B·F)와 자사 과거 등록 특허(in_analysis_set = 0)의 인용은 쓰지 않거나 따로 표시한다.
- 자기 인용은 인용하는 쪽과 인용되는 쪽의 assignee_id(applicants.csv의 첫 출원인)가 같은 것이다. 인용하는 쪽이 정제본 밖이면 citing_docs.csv의 assignee_id를 쓴다.
- 공동출원 네트워크의 노드는 applicants.csv의 assignee_id, 가중치는 함께 낸 패밀리 수다. 자사는 솔베나르소재(SOLVENAR MATERIALS CO., LTD., assignee_id A0001)다.

실습할 때 알아 둘 점

- 출원인의 유형(기업·대학·연구소)은 families.csv의 assignee_type(첫 출원인 기준)에 있다.

## 정제본에서 가져온 파일

아래 파일은 7장의 데이터 사전(책의 표 7.1)대로 만든 정제본이다. 같은 이름의 파일은 어느 폴더에서나 내용이 같다.

| 파일 | 내용 |
|---|---|
| families.csv | 패밀리 마스터. 한 행이 패밀리 한 건(정제본 5,199건 + 자사 과거 등록 특허 60건). 대표 문서, 날짜, 출원인, CPC, sub_tech, 인용 수, norm_cit(참고판), legal_status, 심사 이력, 발명자, 만료일, in_analysis_set |
| citations.csv | 인용 쌍(문서 단위). 정제본이 인용한 것과 정제본을 인용한 것 전부. 패밀리 단위로 집계하려면 (citing_family_id, cited_family_id)로 중복을 제거한다 |
| applicants.csv | 패밀리×출원인. 공동 출원인을 모두 담은 긴 형식 |
| citing_docs.csv | citations.csv의 citing_doc_id에 나오는 모든 문서(정제본 안팎)의 날짜·출원인. 8장 폴더와 같은 파일 |

families.csv를 쓸 때의 주의

- in_analysis_set이 1인 5,199건이 분석 대상인 정제본(미국 우선 연도 2014~2023년)이다. 0인 60건은 자사(솔베나르소재, SOLVENAR MATERIALS CO., LTD., assignee_id A0001)의 과거 미국 등록 특허(우선 2006~2013년)이며, 연도별 비교·점유율·네트워크에는 넣지 않는다.
- norm_cit는 참고판 값이다. 등록 후 5년 인용 창이 기준일 안에 끝난 등록 패밀리만 값이 있고, 분모는 같은 우선 연도·같은 세부 기술의 평균이다.
- legal_status(pending, abandoned, in_force, grace, lapsed, expired)는 반드시 status_as_of(2025-06-30)와 함께 읽는다.
- 미국 문서가 없는 패밀리 1건(비공개 요청, 심사 중)은 rep_pub_number가 비어 있다.
