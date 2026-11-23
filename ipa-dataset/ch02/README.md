# ch02 — 패밀리 30건의 출원 경로·기한·만료일 감사 (소과제 2)

자료 기준일 2025-06-30. 이 데이터셋의 모든 기업·기관·특허·번호·인물·텍스트는 교육용으로 만든 가상의 것이며 실제 특허가 아니다.

이 폴더의 파일만으로 2장의 프롬프트와 소과제를 할 수 있다. 다른 폴더의 파일은 필요 없다.

- prompts.md: 2장의 프롬프트 1개. 준비, 먼저 보낼 말, 복사해 보낼 프롬프트를 차례로 적었다. 준비와 프롬프트는 책에 실린 글과 같다. 모두 대화형 AI에 이 폴더의 파일을 업로드해 따라 할 수 있고, 외부 서비스가 필요하거나 알아 둘 점이 있는 프롬프트에는 "진행 방법"을 더했다.
- data_dictionary.csv: 이 폴더 파일의 열 정의(file, column, type, description_ko, example).

## 파일 표

| 파일 | 행 수 | 열 수 | 구분 |
|---|---:|---:|---|
| family_docs.csv | 176 | 9 | 이 장의 입력 파일 |
| pct_data.csv | 30 | 7 | 이 장의 입력 파일 |
| priority_claims.csv | 117 | 7 | 이 장의 입력 파일 |

소과제 1은 실제 제품과 KIPRIS·에스파스넷 조회로 하므로 데이터셋을 쓰지 않는다. 소과제 2는 아래 세 파일을 쓴다.

| 파일 | 행 | 내용 |
|---|---:|---|
| family_docs.csv | 176 | 자사 패밀리 30건의 관청별 문헌 목록(US·EP·JP·CN·KR·WO). 열: family_id, office, pub_number, pub_id, kind, pub_date, appl_number, appl_id, appl_date. 공개 문헌이 아직 없는 출원은 pub_number·kind·pub_date가 빈값이고 출원번호·출원일만 있다 |
| priority_claims.csv | 117 | 위 문헌의 출원이 주장한 우선권(office+appl_id → prior_office, prior_number, prior_date, prior_kind) |
| pct_data.csv | 30 | PCT 경로 패밀리의 국제출원일·국제공개·관청별 국내단계 진입일. office가 빈 행은 기준일에 아직 진입하지 않은 국제출원 |

열 설명
- appl_date: 그 관청의 출원일. PCT 국내단계 출원은 국제출원일이 적혀 있고, 진입일은 pct_data.csv의 entry_date다.
- prior_kind: paris(파리 우선권), pct(국제출원을 바탕으로 한 국내단계), provisional(미국 가출원), continuation·division(미국 계속·분할 출원).
- kind: 미국 A1(공개)·B1(공개 없이 등록)·B2(등록), EP A1/B1, JP A/B2, CN A/B, KR A(공개)/B1(등록), WO A1.

주의
- 경로·기한 준수 여부·공개 예정일·만료일은 파일에 없다. 날짜만으로 계산하는 것이 과제다.
- 우선일이 둘 이상인 패밀리, 12개월 기한을 넘긴 출원, 월말 날짜, 비공개 요청, 공개 전 등록, 국내단계 기한이 남은 2023년 PCT 출원이 섞여 있다.
- 한국 최초 출원 패밀리의 한국 출원일이 최초 우선일이다. 미국 가출원(62/… 또는 63/…)은 번호만 있고 문헌은 없다.
- 미국 등록 특허의 존속기간 조정·포기는 이 장에서 반영하지 않는다(4장).

실습할 때 알아 둘 점

- 최초 출원이 아닌데 priority_claims.csv에 우선권 행이 없는 출원이 7건 있다(패밀리 93791228의 CN·EP, 96765077의 CN·EP·JP, 94041863의 EP·JP). 앞의 두 패밀리는 최초 출원일부터의 개월 수로 경로와 기한을 따지고 우선권 기록을 확인 요청 목록에 적는다. 94041863의 EP·JP는 pct_data.csv에 진입일이 있는 PCT 국내단계 출원이다.
- 미국 계속·분할 출원(prior_kind가 continuation·division)의 기본 만료일은 원출원의 출원일부터 20년이다.
