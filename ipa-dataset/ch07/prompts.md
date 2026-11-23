# 7장 프롬프트 — 특허 데이터 정제와 분석 데이터셋 구축

『지식재산분석론』 7장의 프롬프트 3개를 복사해 쓸 수 있게 옮긴 파일이다(실습 데이터셋 ipa-dataset v2.6).

## 쓰는 법

1. "준비"에 적힌 파일을 이 폴더에서 찾아 대화형 AI에 업로드한다. 프로그래밍 환경은 필요 없다.
2. "먼저 보낼 말"이 있으면 복사해 보내고, 답이 준비와 README.md에 적힌 값과 같은지 본다. AI가 파일을 제대로 읽었는지 확인하는 단계다.
3. "프롬프트"를 복사해 같은 대화에 보낸다.

"진행 방법"은 세 가지를 적은 것이다. 외부 서비스가 있어야 하는 프롬프트의 실행 절차, 대화형 AI에서 할 때 알아 둘 점, 실제 자료나 더 큰 규모로 해 볼 때의 선택 절차다. 외부 서비스의 화면과 조건은 바뀌므로 책에는 무엇이 필요한지만 적고 절차는 이 파일에 적었다. 실습 전체의 안내는 최상위 폴더의 SETUP.md에 있다.

준비와 프롬프트는 책에 실린 글과 같다. 책의 프롬프트가 이 폴더의 파일 이름, 열 이름, 연도, 건수로 적혀 있기 때문이다. 책에서 "프롬프트 맨 아래에 붙인다"고 한 표는 이 파일의 프롬프트에 이미 붙여 두었고, 그런 곳은 "책에 더한 것"에 적었다. 책 본문의 표·그림·예제는 예제 데이터(5,400건)의 값이어서 이 폴더의 파일로 얻는 값과 다르다.

---

## 프롬프트 7.1 — 출원인 정규화 사전 만들기

**실행 환경**: 코드 실행이 되는 대화형 AI(파일 첨부)

**진행 방법**

외부 서비스는 필요 없다.

- assignee가 빈 문서 241건 가운데 assignments_raw.csv에 doc_id가 같은 양도 기록이 있는 것은 100건이다. 프롬프트대로 같은 family_id의 다른 문서 값까지 쓰면 대부분 채워진다. 끝까지 빈 건수를 확인한다.
- 편집 거리와 자로-윙클러 유사도를 계산하는 도구가 없다고 하면 "직접 계산하는 함수를 써서 실행해 줘"라고 요청한다. 이름이 수백 개여서 직접 계산해도 바로 끝난다.
- 자로-윙클러 0.9 이상인 후보 쌍은 수백 쌍이 나온다. 모두 합치지 말고 패밀리 수가 많은 이름부터 확인한다.

**준비**

- ① ch07 폴더의 docs_raw_part1.csv, docs_raw_part2.csv, docs_raw_part3.csv(원자료 문서 15열, 조각마다 머리글)와 assignments_raw.csv(양도 기록. 빈 assignee 약 2%의 양수인을 찾는 데 쓴다)를 업로드하고, 세 조각의 행 수 합이 README.md의 파일 표(12,241행)와 같은지, 고유 assignee 수가 몇인지 묻는다.
- ② 파일이 커서 첨부가 안 되면 Excel에서 doc_id·family_id·assignee 세 열만 남겨 업로드한다.

**먼저 보낼 말**

```text
첨부한 docs_raw_part1.csv, docs_raw_part2.csv, docs_raw_part3.csv의 행 수 합과 고유 assignee 수를 알려 줘.
```

**프롬프트**

```text
첨부한 docs_raw_part1.csv~docs_raw_part3.csv를 이어 붙여 family_id·assignee 두 열을 출원인 테이블로 쓰고(assignee가 빈 행은 assignments_raw.csv에서 doc_id로 양수인을 찾아 채우고, 그래도 비면 같은 family_id의 다른 문서 값을 쓴다), assignee별 고유 family_id 수를 집계한 뒤 차례로 처리해 줘. ① 대문자화, 구두점·중복 공백·법인 표기(CO LTD, INC, CORP, GMBH, KK, KABUSHIKI KAISHA, (주)·㈜·주식회사 등) 제거 ② 편집 거리 1 이내이고 패밀리 수가 더 많은 이름이 있으면 그 이름으로 교정하되, 두 표기 모두 패밀리 5건 이상이면 교정하지 말고 후보 쌍으로 ③ 자로-윙클러 유사도 0.9 이상인 쌍은 합치지 말고 후보로만 출력.
결과는 사전(assignee, assignee_std, 적용 단계, 근거), 사람이 확인할 후보 쌍(이름 A, 이름 B, 유사도, 두 이름의 패밀리 수), assignee_std로 바꾼 출원인 테이블의 세 가지로 만들어 줘.
첨부 자료에 없는 정보(계열사, 사명 변경)로 묶은 항목은 "추정"으로 표시해 줘. 단계별 고유 이름 수, 처리 전후 행 수, 사용한 코드와 라이브러리 버전을 함께 보여 줘.
```

---

## 프롬프트 7.2 — 중복 제거와 패밀리 통합

**실행 환경**: 코드 실행이 되는 대화형 AI(파일 첨부)

**준비**

- ① ch07 폴더의 docs_raw_part1.csv·docs_raw_part2.csv·docs_raw_part3.csv → family_docs_raw.csv(관청별 문헌. family_id, office, kind 등) → priority_raw.csv(우선권 목록) → data_dictionary.csv 순서로 업로드한다.
- ② 행 수를 README.md의 파일 표(12,241행, 25,656행, 19,362행)와 대조한다. 파일이 커서 첨부가 안 되면 docs_raw_part1.csv만 업로드해 절차를 확인하고, 프롬프트의 12,241을 그 조각의 행 수(4,081)로 고친다.

**먼저 보낼 말**

```text
첨부한 docs_raw 세 파일의 행 수 합과 family_docs_raw.csv, priority_raw.csv의 행 수를 알려 줘.
```

**프롬프트**

```text
첨부한 docs_raw_part1.csv~docs_raw_part3.csv는 미국 문서 12,241행(세 조각을 이어 붙인다), family_docs_raw.csv와 priority_raw.csv는 패밀리 자료의 family_id별 문서 목록, 우선권 목록, 원출원 정보(계속·부분계속 출원이 이어받은 출원 번호)다. 이를 패밀리 마스터로 바꿔 줘. priority_raw.csv는 family_docs_raw.csv의 문서와 office·appl_id로 이어지고, prior_kind가 continuation·division인 행이 원출원 정보다.
규칙: 모든 열이 같은 중복 행은 먼저 지우고, family_id마다 한 행. rep_pub_number는 kind가 B인 미국 문서, 없으면 가장 먼저 공개된 미국 문서. n_docs는 미국 문서 수, family_size는 관청 코드 고유 개수(WO 제외, EP는 1곳). priority_date는 그 family_id가 직접 주장한 우선권의 가장 이른 날짜(없으면 문서 파일의 가장 이른 출원일), parent_priority_date는 원출원 체인의 가장 이른 우선일(해당 없으면 빈값). title·abstract·first_claim은 대표 문서의 값, first_claim_pub는 가장 이른 미국 공개공보의 청구항 1(없으면 빈값). cpc 열은 분류 테이블(family_id, cpc, 순서)로 나누고 날짜는 YYYY-MM-DD로.
요약으로 입력 행 수, 지운 중복 행 수, 고유 family_id 수, 출력 행 수, n_docs 합계, 대표 문서가 B인 패밀리 수, 날짜 변환 실패 수, priority_date 결측 수와 우선권 목록 때문에 priority_date가 바뀐 패밀리 목록을 주고, 사용한 코드와 라이브러리 버전을 함께 보여 줘.
```

---

## 프롬프트 7.3 — 인용·유지료 사건·파일 래퍼 결합과 검증

**실행 환경**: 코드 실행이 되는 대화형 AI(파일 첨부)

**준비**

- ① 프롬프트 7.2에서 만든 패밀리 마스터와 문서 테이블을 업로드한다. 프롬프트 7.2를 건너뛰었으면 ch07 폴더의 families.csv·documents.csv(정제를 마친 파일)로 대신한다.
- ② 같은 폴더의 citations.csv, maintenance_raw.csv, file_wrapper_raw.csv(포기 ABN, 거절통지 CTNF·CTFR), data_dictionary.csv를 업로드하고 마스터의 행 수를 묻는다(프롬프트 7.2의 결과는 7,586행 안팎, families.csv는 5,259행).
- ③ citations.csv가 커서 첨부가 안 되면 마스터에 있는 패밀리의 행만 추려 업로드한다.

**먼저 보낼 말**

```text
첨부한 패밀리 마스터의 행 수를 알려 줘.
```

**프롬프트**

```text
첨부한 파일은 패밀리 마스터와 문서 테이블(프롬프트 7.2의 결과 또는 families.csv·documents.csv. 문서 테이블은 문서–family_id 대응표로도 쓴다), 인용 파일 citations.csv(등록→등록, 등록→공개), 유지료 사건 파일 maintenance_raw.csv, 파일 래퍼 사건 기록 file_wrapper_raw.csv이며, 열 정의는 data_dictionary.csv에 있다. 기준일은 2025-06-30이다. 존속기간 자료는 따로 없으니 만료일은 earliest_us_filing에 20년을 더한 날로 계산해 줘(families.csv로 대신했으면 expiry_date 열을 쓴다).
마스터 쪽 행을 유지하는 왼쪽 결합으로 차례로 붙여 줘. ① 인용: 번호를 family_id로 바꾸고 같은 패밀리 쌍의 중복과 자기 패밀리 인용을 지워 n_backward·n_forward를 집계(전방 인용은 citations.csv 전체에서). ② 유지료 사건: 등록 번호별 목록으로 묶어 maintenance_events로. ③ 파일 래퍼: n_nonfinal·n_final과 포기 사건 여부. 등록 번호의 쉼표와 앞자리 0, 출원번호의 슬래시·쉼표(28/470,267 → 28470267)는 결합 전에 지우고, 결합 대상의 키가 고유하지 않으면 멈추고 알려 줘.
④ 기준일을 status_as_of로 적고 legal_status를 정해 줘. 등록 문서가 없으면 포기 사건 여부로 abandoned 또는 pending, 있으면 expired → lapsed → grace → in_force의 순서다. ⑤ 평가 시점(예: 3년 뒤)을 주면 그때까지 만료되는 특허, 그 사이 유지료 기한이 오는 특허, pending 패밀리를 나누어 집계해 줘.
결합마다 결합 전후 행 수, 키 일치율, 새 열의 결측 비율을 표로 주고, 첫 납부의 유예 기간이 기준일 전에 끝났는데 사건이 없는 등록 특허 수, legal_status 범주별 건수, 사용한 코드와 라이브러리 버전을 함께 보여 줘.
```
