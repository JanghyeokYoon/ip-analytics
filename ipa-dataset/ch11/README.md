# ch11 — 의미 검색으로 검토 범위 정하기, LLM 분류의 신뢰 판정과 우선 검토 목록

자료 기준일 2025-06-30. 이 데이터셋의 모든 기업·기관·특허·번호·인물·텍스트는 교육용으로 만든 가상의 것이며 실제 특허가 아니다.

이 폴더의 파일만으로 11장의 프롬프트와 소과제를 할 수 있다. 다른 폴더의 파일은 필요 없다.

- prompts.md: 11장의 프롬프트 4개. 준비, 먼저 보낼 말, 복사해 보낼 프롬프트를 차례로 적었다. 준비와 프롬프트는 책에 실린 글과 같다. 모두 대화형 AI에 이 폴더의 파일을 업로드해 따라 할 수 있고, 외부 서비스가 필요하거나 알아 둘 점이 있는 프롬프트에는 "진행 방법"을 더했다.
- data_dictionary.csv: 이 폴더 파일의 열 정의(file, column, type, description_ko, example).

## 파일 표

| 파일 | 행 수 | 열 수 | 구분 |
|---|---:|---:|---|
| brief_similarity.csv | 5,598 | 5 | 이 장의 입력 파일 |
| corpus_part1.jsonl | 2,799 | 7 | 이 장의 입력 파일 |
| corpus_part2.jsonl | 2,799 | 7 | 이 장의 입력 파일 |
| design_brief.md | - | - | 이 장의 입력 파일 |
| embeddings_128d.csv | 5,598 | 129 | 이 장의 입력 파일 |
| embeddings_meta.json | - | - | 이 장의 입력 파일 |
| eval_sample.csv | 100 | 3 | 이 장의 입력 파일 |
| search_result.csv | 187 | 3 | 이 장의 입력 파일 |
| families.csv | 5,259 | 31 | 정제본 |

| 파일 | 행 | 내용 |
|---|---:|---|
| design_brief.md | — | 리튬금속 음극 보호층 설계 설명문(의미 검색의 질의문) |
| corpus_part1.jsonl, corpus_part2.jsonl | 5,598 | 패밀리별 텍스트. 열: family_id, assignee_std, priority_date, cpc, title, abstract, first_claim. 정제본 패밀리 전체에 정제본 밖(셀 구조·이웃 분야) 400건을 더해 두었다 |
| search_result.csv | 187 | 6장 방법으로 만든 검색식의 결과 목록(family_id, pub_number, title). 검색식: ("lithium metal" OR "metallic lithium") AND ("protective layer" OR "protection layer") AND CPC (H01M4/134 OR H01M4/382), 제목+요약+청구항 1에 적용 |
| eval_sample.csv | 100 | 사람 판정. rater_a는 100건 전부, rater_b는 30건. 값은 조성·계면·공정·범위밖 |

안내
- 소과제 1: 요약과 청구항 1을 이어 붙여 임베딩하고 설계 설명문과의 코사인 유사도 상위 100건을 읽는다. 관련 판정 기준은 설계 설명문의 "검토가 필요한 특허의 범위"를 따른다.
- 소과제 2: 네 분류의 정의와 경계 규칙(할라이드 코팅층의 소속 등)을 먼저 적는다. 텍스트 신규성은 앞선 5년 창이 온전한 우선 연도(2019년 이후)의 패밀리마다 그 앞 5년 패밀리와의 최대 유사도로 계산한다.
- 관련성 참값과 네 분류 참값은 파일에 없다.

고정 임베딩
- embeddings_128d.csv(5,598행 × 128차원): corpus_part1.jsonl·corpus_part2.jsonl의 abstract + first_claim을 고정 모델(TF-IDF(1~2-gram, sublinear tf, min_df 3, max_df 0.6, 영어 불용어) + TruncatedSVD 128차원(시드 11), L2 정규화)로 임베딩한 벡터. 임베딩 모델을 돌릴 수 없는 환경에서 소과제 1·2(와 뒤 장의 이웃 밀도)에 쓴다. 모델 정보는 embeddings_meta.json.
- brief_similarity.csv: design_brief.md의 임베딩과 각 패밀리의 코사인 유사도(rank, family_id, assignee_std, priority_date, similarity). 소과제 1 요구사항 1의 상위 100건은 이 파일의 rank 1~100이다. 직접 임베딩을 돌릴 수 있으면 그 결과로 대체하고 두 상위 100건의 겹침을 적는다.

## 정제본에서 가져온 파일

아래 파일은 7장의 데이터 사전(책의 표 7.1)대로 만든 정제본이다. 같은 이름의 파일은 어느 폴더에서나 내용이 같다.

| 파일 | 내용 |
|---|---|
| families.csv | 패밀리 마스터. 한 행이 패밀리 한 건(정제본 5,199건 + 자사 과거 등록 특허 60건). 대표 문서, 날짜, 출원인, CPC, sub_tech, 인용 수, norm_cit(참고판), legal_status, 심사 이력, 발명자, 만료일, in_analysis_set |

families.csv를 쓸 때의 주의

- in_analysis_set이 1인 5,199건이 분석 대상인 정제본(미국 우선 연도 2014~2023년)이다. 0인 60건은 자사(솔베나르소재, SOLVENAR MATERIALS CO., LTD., assignee_id A0001)의 과거 미국 등록 특허(우선 2006~2013년)이며, 연도별 비교·점유율·네트워크에는 넣지 않는다.
- norm_cit는 참고판 값이다. 등록 후 5년 인용 창이 기준일 안에 끝난 등록 패밀리만 값이 있고, 분모는 같은 우선 연도·같은 세부 기술의 평균이다.
- legal_status(pending, abandoned, in_force, grace, lapsed, expired)는 반드시 status_as_of(2025-06-30)와 함께 읽는다.
- 미국 문서가 없는 패밀리 1건(비공개 요청, 심사 중)은 rep_pub_number가 비어 있다.
