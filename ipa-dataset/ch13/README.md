# ch13 — 매각 묶음과 가격, 유지료 생존분석과 예산

자료 기준일 2025-06-30. 이 데이터셋의 모든 기업·기관·특허·번호·인물·텍스트는 교육용으로 만든 가상의 것이며 실제 특허가 아니다.

이 폴더의 파일만으로 13장의 프롬프트와 소과제를 할 수 있다. 다른 폴더의 파일은 필요 없다.

- prompts.md: 13장의 프롬프트 3개. 준비, 먼저 보낼 말, 복사해 보낼 프롬프트를 차례로 적었다. 준비와 프롬프트는 책에 실린 글과 같다. 모두 대화형 AI에 이 폴더의 파일을 업로드해 따라 할 수 있고, 외부 서비스가 필요하거나 알아 둘 점이 있는 프롬프트에는 "진행 방법"을 더했다.
- data_dictionary.csv: 이 폴더 파일의 열 정의(file, column, type, description_ko, example).

## 파일 표

| 파일 | 행 수 | 열 수 | 구분 |
|---|---:|---:|---|
| buyer_assumptions.csv | 15 | 5 | 이 장의 입력 파일 |
| comparable_licenses.csv | 5 | 11 | 이 장의 입력 파일 |
| focal_portfolio.csv | 148 | 10 | 이 장의 입력 파일 |
| us_maintenance_focal.csv | 184 | 7 | 이 장의 입력 파일 |
| families.csv | 5,259 | 31 | 정제본 |
| us_maintenance.csv | 1,661 | 7 | 정제본 |
| fee_schedule.csv | 21 | 9 | 정제본 |
| family_publications.csv | 27,780 | 9 | 정제본 |
| citations.csv | 77,841 | 6 | 정제본 |
| texts_us_part1.jsonl | 1,722 | 8 | 정제본 |
| texts_us_part2.jsonl | 1,722 | 8 | 정제본 |
| texts_us_part3.jsonl | 1,722 | 8 | 정제본 |
| texts_us_part4.jsonl | 1,722 | 8 | 정제본 |
| texts_us_part5.jsonl | 1,718 | 8 | 정제본 |
| citing_docs.csv | 14,094 | 8 | 8장 폴더와 같은 파일 |

| 파일 | 행 | 내용 |
|---|---:|---|
| focal_portfolio.csv | 148 | 자사(솔베나르소재, SOLVENAR MATERIALS CO., LTD.)의 미국 등록 특허 전부: 정제본 C의 등록 특허 88건(등록 2016~2025) + 과거 등록 특허 L 60건(등록 2009~2016, families.csv의 in_analysis_set = 0). 열: patent_id, family_id, sub_tech, grant_date, num_claims, filing_date(만료 기준일), pta_days, td_date, n_forward_other, legal_status |
| us_maintenance_focal.csv | — | 위 특허의 유지료 사건(patent_id로 결합). us_maintenance.csv에서 자사 특허만 뽑은 것 |
| buyer_assumptions.csv | 15 | 매수 후보 3곳 × 2026~2030년 × 두 기준 매출(원). revenue_material_krw는 고체 전해질 소재 매출, revenue_cell_krw는 전고체 셀 매출(소재 전문 기업은 0) |

가정(README에 둔 것)
- 로열티율 범위: 소재 매출 기준 2~5%, 셀 매출 기준 0.3~1.0%(기술기여도 방식의 교차 확인용 소재 원가 비중 가정: 셀 매출의 12~18%).
- 할인율: 10%·12%·15%의 세 값으로 3×3 격자를 만든다.
- 정규화 인용·패밀리 크기는 이 폴더의 families.csv(norm_cit, family_size)에서, 청구항 1과 패밀리 문헌은 texts_us_part1.jsonl ~ texts_us_part5.jsonl과 family_publications.csv에서 가져온다.
- 살아 있는 특허 = legal_status가 in_force 또는 grace. 자사 T2 가운데 살아 있는 등록 특허는 42건이다.
- 만료일 = filing_date + 20년 + pta_days, td_date가 있으면 그 날이 상한. 미국 유지료 요금은 fee_schedule.csv(office = US)의 대기업(large) 기준.

소과제 2(생존분석) 안내
- 유지료 납부 시점은 등록 후 3.5·7.5·11.5년이고 6개월 유예가 붙으므로, 소멸 사건의 시점은 등록 후 4·8·12년이다. 등록 후 12년이 기준일 안에 든 특허는 35건(모두 L), 8년이 든 특허는 61건이다. 정제본 C의 자사 등록 특허(등록 2016년 11월 이후)만으로는 S(8)·S(12)를 추정할 수 없으므로 반드시 L을 포함한다.
- 관측 기간 = 등록일부터 기준일(또는 만료일)까지. 사건 = 회복되지 않은 소멸(EXP. 사건의 effective_date = 유예 기간 끝). EXPX로 회복된 특허(L에 1건)는 소멸로 집계하지 않고 유지 중으로 둔다. 유예 기간 안에 가산금과 함께 낸 경우(M1554가 같은 날 붙음, L에 2건)는 납부다.
- 과거 등록 특허 L의 norm_cit(families.csv)는 2006~2013년 코호트 안에서 계산한 참고치다. 소과제 1의 가치 점수는 살아 있는 특허 전부(C + L)에 매기되, L의 남은 존속기간이 짧다는 점을 해석에 적는다.

비교 계약
- comparable_licenses.csv: 로열티율 근거로 쓰는 가상 비교 계약 5건(분야, 당사자 유형, 독점 여부, 기준 매출 정의, 요율, 선급금, 기간, 근거 유형). 모두 가상이며 실제 계약이 아니다. 소과제 1 요구사항 3은 조건이 가장 가까운 계약을 택하고 차이(독점 여부·기준 매출·분야)를 조정한 근거를 적는다.

실습할 때 알아 둘 점

- 소과제 1에서 묶음 특허를 인용한 출원인은 citations.csv(cited_family_id가 묶음 특허인 행)와 citing_docs.csv(citing_doc_id의 출원인)로 찾는다.
- us_maintenance.csv의 patent_id는 families.csv의 rep_doc_id(대표 등록 문서의 번호)와 결합한다.

## 정제본에서 가져온 파일

아래 파일은 7장의 데이터 사전(책의 표 7.1)대로 만든 정제본이다. 같은 이름의 파일은 어느 폴더에서나 내용이 같다.

| 파일 | 내용 |
|---|---|
| families.csv | 패밀리 마스터. 한 행이 패밀리 한 건(정제본 5,199건 + 자사 과거 등록 특허 60건). 대표 문서, 날짜, 출원인, CPC, sub_tech, 인용 수, norm_cit(참고판), legal_status, 심사 이력, 발명자, 만료일, in_analysis_set |
| us_maintenance.csv | 정제본과 자사 과거 등록 특허의 유지료 사건. M1551 등 납부, M1554 등 가산금 납부, EXP. 소멸, EXPX 회복 |
| fee_schedule.csv | 요금표. 한국 연차료(2023-08-01 전후 두 기간)와 미국 유지료 |
| family_publications.csv | 패밀리×관청 문헌. US·EP·JP·CN·KR·WO 문헌의 번호·날짜·출원번호 |
| citations.csv | 인용 쌍(문서 단위). 정제본이 인용한 것과 정제본을 인용한 것 전부. 패밀리 단위로 집계하려면 (citing_family_id, cited_family_id)로 중복을 제거한다 |
| texts_us_part1.jsonl ~ texts_us_part5.jsonl | 정제본과 자사 과거 등록 특허의 미국 문서 텍스트(제목·요약·청구항 전체·청구항 1). 다섯 조각을 이어 붙이면 한 파일이다. 공개(A1)와 등록(B1/B2) 문서의 청구항이 다를 수 있다 |
| citing_docs.csv | citations.csv의 citing_doc_id에 나오는 모든 문서(정제본 안팎)의 날짜·출원인. 8장 폴더와 같은 파일 |

families.csv를 쓸 때의 주의

- in_analysis_set이 1인 5,199건이 분석 대상인 정제본(미국 우선 연도 2014~2023년)이다. 0인 60건은 자사(솔베나르소재, SOLVENAR MATERIALS CO., LTD., assignee_id A0001)의 과거 미국 등록 특허(우선 2006~2013년)이며, 연도별 비교·점유율·네트워크에는 넣지 않는다.
- norm_cit는 참고판 값이다. 등록 후 5년 인용 창이 기준일 안에 끝난 등록 패밀리만 값이 있고, 분모는 같은 우선 연도·같은 세부 기술의 평균이다.
- legal_status(pending, abandoned, in_force, grace, lapsed, expired)는 반드시 status_as_of(2025-06-30)와 함께 읽는다.
- 미국 문서가 없는 패밀리 1건(비공개 요청, 심사 중)은 rep_pub_number가 비어 있다.
