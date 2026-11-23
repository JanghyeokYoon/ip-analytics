# 14장 프롬프트 — 특허 전략: 포트폴리오, 경쟁정보, 분쟁 위험

『지식재산분석론』 14장의 프롬프트 3개를 복사해 쓸 수 있게 옮긴 파일이다(실습 데이터셋 ipa-dataset v2.6).

## 쓰는 법

1. "준비"에 적힌 파일을 이 폴더에서 찾아 대화형 AI에 업로드한다. 프로그래밍 환경은 필요 없다.
2. "먼저 보낼 말"이 있으면 복사해 보내고, 답이 준비와 README.md에 적힌 값과 같은지 본다. AI가 파일을 제대로 읽었는지 확인하는 단계다.
3. "프롬프트"를 복사해 같은 대화에 보낸다.

"진행 방법"은 세 가지를 적은 것이다. 외부 서비스가 있어야 하는 프롬프트의 실행 절차, 대화형 AI에서 할 때 알아 둘 점, 실제 자료나 더 큰 규모로 해 볼 때의 선택 절차다. 외부 서비스의 화면과 조건은 바뀌므로 책에는 무엇이 필요한지만 적고 절차는 이 파일에 적었다. 실습 전체의 안내는 최상위 폴더의 SETUP.md에 있다.

준비와 프롬프트는 책에 실린 글과 같다. 책의 프롬프트가 이 폴더의 파일 이름, 열 이름, 연도, 건수로 적혀 있기 때문이다. 책에서 "프롬프트 맨 아래에 붙인다"고 한 표는 이 파일의 프롬프트에 이미 붙여 두었고, 그런 곳은 "책에 더한 것"에 적었다. 책 본문의 표·그림·예제는 예제 데이터(5,400건)의 값이어서 이 폴더의 파일로 얻는 값과 다르다.

---

## 프롬프트 14.1 — 포트폴리오 매트릭스와 영역 유지 비율

**실행 환경**: 코드 실행이 되는 대화형 AI(파일 첨부)

**준비**

- ① ch14 폴더의 families.csv(family_id, priority_date, sub_tech, assignee_std, norm_cit, in_analysis_set)·applicants.csv(family_id, assignee_std, assignee_sequence), topic_assignment.csv(family_id, topic_id)·topic_scores.csv(topic_id, sub_tech, cagr_10y, novelty_score, opportunity_score)·README.md를 업로드한다.
- ② families.csv의 행 수(5,259행)와 in_analysis_set이 1인 행 수(5,199행)를 묻는다. 자사는 SOLVENAR MATERIALS CO., LTD.(솔베나르소재)다.

**먼저 보낼 말**

```text
첨부한 families.csv의 행 수와 in_analysis_set이 1인 행의 수를 알려 줘.
```

**프롬프트**

```text
첨부한 families.csv와 topic_assignment.csv(family_id, topic_id)로 sub_tech마다 topic_scores.csv의 opportunity_score가 가장 높은 주제를 대표 주제로 두고, 그 주제의 priority_date 기준 2014~2023년 CAGR(시작·끝 각 3년 평균, T = 7)과 topic_scores.csv의 novelty_score를 구해 줘. 매력도와 선두 출원인 건수는 in_analysis_set이 1인 행으로 계산해.
applicants.csv(family_id, assignee_std, assignee_sequence)의 첫 출원인으로 세부 기술별 자사(SOLVENAR MATERIALS CO., LTD.) 패밀리 수, 자사를 뺀 최다 출원인과 그 건수, 상대 포지션, 자사 특허의 norm_cit 중앙값을 구하고(자사 norm_cit 값이 없는 세부 기술은 "자료 부족"으로 표시), 네 지표를 최소–최대 정규화해 축별로 같은 가중치로 평균한 뒤 경계 0.5로 영역을 붙여 줘.
두 축의 가중치를 각각 0.3~0.7(0.1 단위)로 바꾼 25가지 조합의 영역 유지 비율을 표로 만들고, 2 × 2 산점도에 0.8 미만 기술과 상대 포지션 1 이상 기술을 표시해 줘. 사용한 코드와 라이브러리 버전을 함께 보여 줘.
```

---

## 프롬프트 14.2 — 월간 경보 판정과 오경보율

**실행 환경**: 코드 실행이 되는 대화형 AI(파일 첨부)

**진행 방법**

외부 서비스는 필요 없다.

- updates.csv에는 발명자 열이 없다. 신규 발명자 조건은 families.csv의 inventor_ids와 priority_date로 계산하게 한다.
- new_citations.csv의 citing_assignee_raw 가운데 4%쯤은 정제본 밖 출원인이어서 표준명으로 바뀌지 않는다. 경쟁사 4곳의 집계에는 영향이 없다.

**준비**

- ① ch14 폴더의 families.csv, applicants.csv, documents.csv, updates.csv(12개 분기의 신규 문서), new_citations.csv, new_assignments.csv, README.md를 업로드하고(열 정의는 data_dictionary.csv) updates.csv의 행 수(4,149행)와 release_quarter 범위(2022Q3~2025Q2)를 묻는다.
- ② 프롬프트 맨 아래에 표 14.3을 붙인다(ch14 폴더 prompts.md의 프롬프트에는 붙어 있다).

**먼저 보낼 말**

```text
첨부한 updates.csv의 행 수와 release_quarter의 범위를 알려 줘.
```

**프롬프트**

```text
첨부한 families.csv와 추가분(updates.csv, new_citations.csv. release_quarter의 마지막 4개 분기가 판정 대상, 그 앞 8개 분기가 기준 기간), applicants.csv로 경쟁사 4곳(자사 SOLVENAR MATERIALS CO., LTD.를 뺀 상위 4곳)에 맨 아래에 붙인 경보 조건(표 14.3)을 판정해 줘(필수특허 선언·국가별 상태 조건은 뺀다). updates.csv의 assignee_raw는 applicants.csv로, new_citations.csv의 citing_assignee_raw는 documents.csv·applicants.csv로 만든 원표기–표준명 대응표로 정규화해. 자사 특허 인용은 new_citations.csv에서 cited_family_id가 자사 패밀리인 행을 인용한 쪽 출원인별로 집계하고, 양도 조건은 new_assignments.csv에서 convey_type이 assignment·merger이고 assignor가 경쟁사인 기록으로 판정해.
자사 특허 인용은 직전 8분기, 신규 발명자는 직전 2년의 평균 λ로 P(X ≥ c) ≤ 0.05인 가장 작은 c를 임계값으로 쓰고, 경쟁사·조건마다 λ, c, 한 기간과 한 해의 오경보율, 분산 ÷ 평균을 한 표로 보여 줘.
경보마다 조건, family_id, 출원인, priority_date, title, 근거 수치를 표로 만들고, 읽을 특허 10건을 추려 abstract의 근거 문장을 붙인 두 문장 요약을 써 줘. 사용한 코드와 라이브러리 버전을 함께 보여 줘.

경보 조건(책의 표 14.3. 조건 | 데이터 열 | 임계값의 예):
- 새 CPC 진입 | 출원인 테이블(assignee_std), cpc, priority_date | 최근 12개월 첫 출원 서브그룹 3건 이상
- 신규 발명자 급증 | 출원인 테이블, inventor_ids, priority_date | 12개월 신규 발명자 ≥ 2년 연평균의 포아송 상위 5% 값
- 자사 특허 인용 | backward_citations 중 자사 family_id | 분기 인용 ≥ 8분기 평균의 포아송 상위 5% 값
- 우선심사·조기 등록 | filing_date, grant_date, n_nonfinal | 평균 심사의 절반 미만(미국 12개월, 한국 우선심사 별도)
- 해외 패밀리 확대 | family_size, 국가별 상태 테이블의 office | 직전 갱신보다 관청이 2곳 이상 늘어난 패밀리
- 특허 양도 | 양도 데이터셋의 양도·합병 기록, rep_pub_number | 경쟁사 특허의 타사 양도 1건 이상(특허관리전문회사 포함)
- 필수특허 선언 | 필수특허 선언 목록, assignee_std | 경쟁사의 신규 선언 1건 이상
- 소멸 특허의 회복 | office_status(lapsed → in_force) | FTO 후보 중 소멸 특허의 회복 1건 이상
```

**책에 더한 것**: 책의 표 14.3을 프롬프트 맨 아래에 붙여 두었다.

---

## 프롬프트 14.3 — FTO 후보 압축과 청구항 대조표

**실행 환경**: 코드 실행이 되는 대화형 AI(파일 첨부)

**진행 방법**

외부 서비스는 필요 없다. 본문 예제의 의미 검색은 문장 임베딩 모델의 유사도 0.60 이상을 후보로 삼았다. 대화형 AI의 코드 실행기에서는 그 모델을 돌리기 어려워, 책의 프롬프트는 TF-IDF를 128차원으로 줄인 벡터의 유사도 상위 40건을 쓴다. 이 벡터의 유사도는 값의 범위가 달라 0.60이라는 임계값을 그대로 쓸 수 없다.

- 파일이 많아 한 번에 첨부되지 않으면 families.csv, family_publications.csv, product_spec.md를 먼저, texts_us_part1.jsonl~texts_us_part5.jsonl을 다음에 업로드한다.
- 청구항 대조는 후보가 많으면 한 번에 끝나지 않는다. "후보 목록까지", "다음은 권리 상태", "다음은 상위 10건의 청구항 대조"처럼 나누어 요청한다.

**준비**

- ① ch14 폴더의 families.csv(family_id, sub_tech, cpc, title, legal_status, status_as_of, expiry_date), texts_us_part1.jsonl~texts_us_part5.jsonl(미국 전문. doc_id, family_id, kind, abstract, first_claim), family_publications.csv(family_id, office, kind, pub_date), product_spec.md를 업로드하고 families.csv의 행 수를 묻는다. 한 번에 첨부되지 않으면 두 번에 나누어 업로드한다.
- ② 프롬프트 맨 아래에 표 14.5(FTO 위험 판정 행렬)를 붙인다. ch14 폴더 prompts.md의 프롬프트에는 이 표가 붙어 있다. 검색식을 직접 적었으면 프롬프트의 "구성요소 용어로 만든 검색식(검색식을 먼저 적는다)"을 "맨 아래에 붙인 검색식"으로 바꾸고 함께 붙인다. 본문 예제의 의미 검색은 문장 임베딩 모델의 유사도 0.60 이상이고, 실습은 대화형 AI가 만들 수 있는 벡터의 유사도 상위 40건이다.

**먼저 보낼 말**

```text
첨부한 families.csv의 행 수를 알려 줘.
```

**프롬프트**

```text
첨부한 families.csv와 texts_us_part1.jsonl~texts_us_part5.jsonl에서 product_spec.md의 제품 정의(구성요소, 공급 형태, 출시 국가. 설명문은 사양 표를 영어 한 문단으로 옮겨 쓴다)를 기준으로 ① 구성요소 용어로 만든 검색식(검색식을 먼저 적는다)에 해당하는 패밀리와 ② 설명문과 abstract·first_claim을 같은 방법으로 임베딩해(TF-IDF를 128차원으로 줄인 벡터, 시드 11) 코사인 유사도가 높은 상위 40건을 합쳐 후보 목록을 만들고, 어느 쪽에서 잡혔는지 표시해 줘.
후보마다 출시 국가(US, KR, EP)의 상태와 status_as_of를 붙이고(미국은 families.csv의 legal_status·status_as_of, 한국·유럽은 family_publications.csv의 office별 공보 유무로 본다), 모든 출시 국가에서 권리가 없는 패밀리는 따로 모아 줘.
남은 패밀리의 first_claim(심사 중이면 kind가 A1인 문서의 first_claim)을 구성요소로 나누어 자사 제품 정의(청구 대상이 셀·전극이면 고객 제품 정의)와 대조하고, 구성요소마다 일치·균등 검토·불일치와 근거 문구, 맨 아래에 붙인 판정 행렬(표 14.5)에 따른 나라별 등급 초안을 적어 줘. 판정은 초안이며 전문가가 확인한다고 표에 적고, 사용한 코드와 라이브러리 버전을 함께 보여 줘.
한 번에 끝내기 어려우면 단계를 나누어 차례로 실행하고, 단계마다 중간 결과를 보여 줘.

FTO 위험 판정 행렬(책의 표 14.5):
청구항 대조 ＼ 권리 상태 | 유효, 강한 무효 자료 없음 | 유효, 강한 무효 자료 후보 있음 | 심사 중 | 최근 소멸 (회복 가능) | 만료·무효·소멸 (회복 불가 확정)
문언 일치(요소 완비) | 높음 | 중간 | 조건부 높음 | 낮음·추적 | 없음
균등·용어 해석 불확실 | 중간 | 낮음 | 추적 | 낮음·추적 | 없음
불일치(균등도 어려움) | 없음 | 없음 | 추적 | 없음 | 없음
```

**책에 더한 것**: 책의 표 14.5를 프롬프트 맨 아래에 붙여 두었다.
