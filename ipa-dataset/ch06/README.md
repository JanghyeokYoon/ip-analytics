# ch06 — 검색식의 품질 추정과 확정, 범위 A·B·C와 시장 경계

자료 기준일 2025-06-30. 이 데이터셋의 모든 기업·기관·특허·번호·인물·텍스트는 교육용으로 만든 가상의 것이며 실제 특허가 아니다.

이 폴더의 파일만으로 6장의 프롬프트와 소과제를 할 수 있다. 다른 폴더의 파일은 필요 없다.

- prompts.md: 6장의 프롬프트 3개. 준비, 먼저 보낼 말, 복사해 보낼 프롬프트를 차례로 적었다. 준비와 프롬프트는 책에 실린 글과 같다. 모두 대화형 AI에 이 폴더의 파일을 업로드해 따라 할 수 있고, 외부 서비스가 필요하거나 알아 둘 점이 있는 프롬프트에는 "진행 방법"을 더했다.
- data_dictionary.csv: 이 폴더 파일의 열 정의(file, column, type, description_ko, example).

## 파일 표

| 파일 | 행 수 | 열 수 | 구분 |
|---|---:|---:|---|
| known_items.csv | 30 | 4 | 이 장의 입력 파일 |
| practice_set.csv | 10 | 7 | 이 장의 입력 파일 |
| search_corpus_part1.jsonl | 4,666 | 10 | 이 장의 입력 파일 |
| search_corpus_part2.jsonl | 4,666 | 10 | 이 장의 입력 파일 |
| search_corpus_part3.jsonl | 4,666 | 10 | 이 장의 입력 파일 |

| 파일 | 행 | 내용 |
|---|---:|---|
| search_corpus_part1.jsonl ~ search_corpus_part3.jsonl | 13,998 | 검색 대상(미국 패밀리). 열: family_id, pub_number(대표 문서), priority_date, pub_date, assignee_raw, assignee_db_group, cpc, title, abstract, first_claim |
| known_items.csv | 30 | 연구소가 검색식과 독립적으로 정한 핵심 특허 목록(family_id, pub_number, title, source) |

열 설명
- 검색식은 title + abstract + first_claim(청구항 1)과 cpc에 적용한다. 대소문자와 하이픈("solid-state"와 "solid state")은 같게 다룬다.
- assignee_db_group은 검색 데이터베이스가 제공하는 출원인 묶음의 근사치다. 대소문자·구두점·법인 표기만 정리했고, 번역명·옛 사명·오타·자회사는 묶지 않았다(정제는 7장).
- 검색 대상에는 고체 산화물 연료전지, 고체 전해 커패시터, 고분자 전해질 단독, 황화물 양극 활물질, 액체 전해질·첨가제, 센서, 전고체 셀 구조·공정 문서가 섞여 있다.

주의
- 관련성 참값은 없다. 표본은 기술 정의로 직접 판정한다.
- 핵심 특허 30건 가운데 일부는 표준 용어 대신 다른 표현이나 화학식만 쓴다.
- 파일이 커서 `_part1`, `_part2`, …로 나뉘어 있다. 모두 읽어 이어 붙인다.

연습용 파일
- practice_set.csv: 소과제 1의 표본 판정 전에 팀이 기준을 맞추는 연습용 10건(관련 4, 무관 5 — 연료전지·황화물 양극 활물질·액체 첨가제·고분자 단독·커패시터, 경계 1). 열: family_id, pub_number, title, abstract, judgment, category_note, reason. 이 10건은 표본 50건에 넣지 않는다.

실습할 때 알아 둘 점

- 검색식을 파일에 적용할 때는 규칙을 먼저 적는다(대소문자 무시, 하이픈과 공백을 같은 것으로 볼지, 복수형을 포함할지). 규칙에 따라 건수가 수백 건씩 달라지므로 보고서에 규칙을 함께 적는다.
