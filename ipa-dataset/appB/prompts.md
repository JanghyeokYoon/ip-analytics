# 부록 B 프롬프트 — 한국·유럽 특허 데이터의 구조

『지식재산분석론』 부록 B의 프롬프트 2개를 복사해 쓸 수 있게 옮긴 파일이다(실습 데이터셋 ipa-dataset v2.6).

## 쓰는 법

1. "준비"에 적힌 파일을 이 폴더에서 찾아 대화형 AI에 업로드한다. 프로그래밍 환경은 필요 없다.
2. "먼저 보낼 말"이 있으면 복사해 보내고, 답이 준비와 README.md에 적힌 값과 같은지 본다. AI가 파일을 제대로 읽었는지 확인하는 단계다.
3. "프롬프트"를 복사해 같은 대화에 보낸다.

"진행 방법"은 세 가지를 적은 것이다. 외부 서비스가 있어야 하는 프롬프트의 실행 절차, 대화형 AI에서 할 때 알아 둘 점, 실제 자료나 더 큰 규모로 해 볼 때의 선택 절차다. 외부 서비스의 화면과 조건은 바뀌므로 책에는 무엇이 필요한지만 적고 절차는 이 파일에 적었다. 실습 전체의 안내는 최상위 폴더의 SETUP.md에 있다.

준비와 프롬프트는 책에 실린 글과 같다. 책의 프롬프트가 이 폴더의 파일 이름, 열 이름, 연도, 건수로 적혀 있기 때문이다. 책에서 "프롬프트 맨 아래에 붙인다"고 한 표는 이 파일의 프롬프트에 이미 붙여 두었고, 그런 곳은 "책에 더한 것"에 적었다. 책 본문의 표·그림·예제는 예제 데이터(5,400건)의 값이어서 이 폴더의 파일로 얻는 값과 다르다.

---

## 프롬프트 B.1 — 국가별 상태 테이블 조립(KIPRIS Plus 파일 + OPS)

**실행 환경**: 코드 실행이 되는 대화형 AI(파일 첨부). 실제 자료는 OPS·KIPRIS Plus(외부 서비스)

**진행 방법**

이 폴더의 실습 파일로 할 때는 외부 서비스가 필요 없다(OPS 조회는 family_publications.csv로 대신한다). 아래는 자기 특허 목록으로 실제 자료를 조회할 때의 준비다.

- 실습 파일로 하면 family_publications.csv의 관청 수가 family_size보다 작은 패밀리가 100건 나온다(우선 2022~2023년). 출원은 했지만 기준일까지 공보가 나오지 않은 관청이 있기 때문이다. 오류가 아니므로 "공보 없음"으로 따로 적게 한다.
- 한국 행의 판정은 최종 납부 연차의 다음 기한(등록일 + 납부한 연차 수), 추가납부 6개월, 회복 신청 3개월의 순서로 한다.

**가. 유럽특허청 OPS(Open Patent Services)**

1. OPS 개발자 포털에서 등록한다(https://developers.epo.org/user/register). 등록한 이용자는 정해진 용량까지 무료로 쓴다. 유럽특허청의 안내로는 비과금 이용자의 한도가 주당 4 GB이며, 정확한 조건은 OPS 이용 약관에서 확인한다.
2. 로그인한 뒤 개발자 콘솔의 "My Apps"에서 앱을 등록하면 OAuth 인증에 쓰는 키와 비밀값을 받는다.
3. 키와 비밀값은 코드에 적지 않고 환경 변수에 넣는다(최상위 폴더 SETUP.md의 5절과 같은 방법).
4. 프롬프트를 보낼 때 "OPS의 키와 비밀값은 환경 변수 ○○, ○○에 있다"고 알려 준다. 받은 코드가 토큰을 먼저 받고, 패밀리 조회와 법적 사건 조회를 호출 간격을 두고 실행하는지 본다.
5. 포털의 API 화면에서 문헌 한 건으로 먼저 호출해 응답 형식을 확인한다.

**나. KIPRIS Plus(한국 특허 자료의 API·대량 자료)**

6. KIPRIS Plus(https://plus.kipris.or.kr)에 가입하고, 필요한 서비스(특허 서지, 등록사항, 행정처리 이력)의 이용을 신청해 인증키를 받는다.
7. 무료 호출 한도와 유료 조건은 서비스마다 다르다. 같은 API의 일부는 공공데이터포털(https://www.data.go.kr)에서도 활용 신청을 할 수 있다.
8. 수천 건 이상은 API 호출보다 대량 자료(파일) 신청이 맞다. 받은 파일의 열 이름을 이 폴더 kr_patents.csv, kr_annual_fees.csv, kr_admin_events.csv, kr_rights_events.csv의 열에 대응시킨다.

내 PC의 Python 준비는 SETUP.md의 4절에 있고, 패키지는 `pip install requests pandas`로 설치한다. 이 절차는 2026년 11월에 확인한 것이다. 화면 이름, 한도, 요금은 바뀔 수 있으니 다르면 서비스의 공식 안내를 따른다.

**준비**

- ① appB 폴더의 families.csv(패밀리 마스터: family_id, family_size, rep_pub_number, legal_status, status_as_of), kr_patents.csv(한국 특허 서지·등록사항: appl_no, appl_id, reg_id, reg_date, family_id, status_kr, last_paid_year, extinct_date, extinct_reason 등), kr_annual_fees.csv(연차별 납부: reg_id, year_from, year_to, paid_date), kr_admin_events.csv(행정처리 이력: appl_id, stage, doc_name, event_date, status), kr_rights_events.csv(권리 변동·회복: reg_id, event_date, event_type, party)와 data_dictionary.csv(열 정의)를 업로드한다. 이어 AI에게 families.csv의 행 수와 열 이름을 물어 파일을 읽었는지 확인한다. 기준일은 자료 기준일 2025-06-30이다.
- ② 실습 번호는 가상 번호라 OPS로 조회되지 않는다. 같은 폴더의 family_publications.csv(패밀리 구성 문헌: family_id, office, pub_number, kind, appl_number, appl_date)도 업로드하고, 프롬프트 첫머리에 "1단계의 OPS 패밀리 조회는 첨부한 family_publications.csv로 대신하고 3단계(EP 법적 사건)는 건너뛰어 줘. 기준일은 2025-06-30이다."를 덧붙인다(appB 폴더 prompts.md의 프롬프트에는 들어 있다). 실무에서는 자기 패밀리 목록과 KIPRIS Plus 등록사항·행정처리 이력을 업로드하고 OPS 인증 정보를 준비한다(신청 절차는 appB 폴더 prompts.md).

**먼저 보낼 말**

```text
첨부한 families.csv의 행 수와 열 이름을 알려 줘.
```

**프롬프트**

```text
1단계의 OPS 패밀리 조회는 첨부한 family_publications.csv로 대신하고 3단계(EP 법적 사건)는 건너뛰어 줘. 기준일은 2025-06-30이다.

첨부한 families.csv는 분석용 데이터셋의 family_id·family_size 목록과 각 패밀리의 미국 대표 공보번호·미국 법적 상태(legal_status)다. 한국 자료로는 KIPRIS Plus에서 받은 등록사항·행정처리 이력 파일(kr_patents.csv, kr_annual_fees.csv, kr_admin_events.csv, kr_rights_events.csv)을 첨부했고, 유럽 자료는 OPS를 호출해 받는다(인증 정보는 내게 있다). 먼저 첨부 파일의 머리글을 읽고 관청마다 결합에 쓸 키와 번호 형식의 변환 규칙을 표로 정리해 줘.
1단계: 미국 공보번호로 OPS 패밀리 서비스를 호출해 family_id마다 구성원 문헌 목록(관청, 출원번호, 공보번호, 종류 코드)을 만들어 줘. 2단계: 한국 번호는 숫자만 남긴 13자리로 바꾸어 등록사항·행정처리 이력과 결합하고, 기준일(YYYY-MM-DD)을 인자로 받아 최종 납부 연차, 추가납부 기간 6개월, 회복 신청 기간 3개월로 판정해 줘. 3단계: EP 문헌마다 OPS 법적 사건 서비스를 호출해 사건 코드·날짜·나라를 받고, 코드를 INPADOC 범주로 바꾸어 F(권리 부여)·H(권리 소멸)·K(권리 회복)의 순서와 기준일로 나라별 상태를 정한 뒤, 단일특허이거나 한 나라 이상에서 유지 중이면 in_force로 두고 유지 중인 나라 수를 status_source에 적어 줘.
결과는 family_id와 관청의 짝이 한 행인 국가별 상태 테이블(family_id, office, appln_number, pub_number, grant_date, office_status, status_as_of, status_source, owner)이고, office_status는 pending, abandoned, in_force, grace, lapsed, expired, revoked 가운데 하나다. 미국 행의 office_status는 첨부한 legal_status를 그대로 옮겨 줘. OPS 호출은 요청 사이에 간격을 두고 100건마다 중간 결과를 저장하며, 한도 초과 응답이 오면 멈추었다가 이어서 받게 해 줘.
패밀리마다 office 값의 개수가 family_size와 같은지, 결합하지 못한 번호가 몇 개인지, 관청별 office_status의 분포를 표로 출력해 줘. 판정 규칙이 적용되지 않은 행은 따로 목록으로 내고, 사용한 파일의 판과 다운로드한 날짜, OPS 호출 날짜, 라이브러리 버전도 함께 적어 줘.
한 번에 끝내기 어려우면 단계를 나누어 차례로 실행하고, 단계마다 중간 결과를 보여 줘.
```

**책에 더한 것**: 실습 번호는 가상 번호라 OPS로 조회되지 않으므로, 책의 준비 ②에 적힌 문장을 프롬프트 첫머리에 넣어 두었다. 그 아래는 책과 같다.

---

## 프롬프트 B.2 — PATSTAT으로 단순·확장 패밀리 대조(PATSTAT이 있는 독자용)

**실행 환경**: 코드를 받아 PATSTAT을 쓸 수 있는 환경에서 실행(이용 경로는 appB 폴더 prompts.md)

**진행 방법**

이 프롬프트의 SQL을 쓰는 데까지는 대화형 AI로 한다. 실행은 대화형으로 바꿀 수 없다. 유럽특허청의 통계용 특허 데이터베이스 PATSTAT이 있어야 하고, 실습 번호는 가상 번호여서 PATSTAT에 없으므로 자기 번호 목록으로 실행한다.

**가. PATSTAT을 쓰는 경로**

1. 경로는 세 가지다. (가) PATSTAT Online: 웹 화면에서 SQL을 실행한다. (나) PATSTAT 대량 자료: 기관의 서버에 넣어 쓴다. (다) 유럽특허청의 Technology Intelligence Platform(TIP): 웹 브라우저의 노트북 환경에서 PATSTAT을 쓴다. 2026년 9월의 보도에 따르면 TIP에서는 PATSTAT을 무료로 쓸 수 있다.
2. 가입 방법과 이용 조건은 유럽특허청의 PATSTAT 안내(https://www.epo.org/en/about-us/observatory-patents-and-technology/observatory-tools/patstat)와 TIP 안내(https://www.epo.org/en/searching-for-patents/data/technology-intelligence-platform)에서 확인한다. 소속 기관이 이미 쓰고 있는지도 알아본다.

**나. SQL 받기**

3. PATSTAT 데이터 카탈로그(PDF)에서 TLS201_APPLN, TLS204_APPLN_PRIOR, TLS211_PAT_PUBLN 테이블의 열 정의를 복사해 텍스트 파일로 저장한다.
4. 자기 번호 목록(family_id, rep_pub_number 두 열과 같은 모양)과 열 정의를 대화형 AI에 업로드하고 프롬프트를 보낸다. 어느 환경에서 실행할지(PATSTAT Online, 자체 서버, TIP)를 함께 알려 준다. 환경마다 SQL 문법이 조금씩 다르기 때문이다.

**다. 실행과 확인**

5. 번호 목록이 길면 먼저 10건으로 SQL을 실행해 결과의 열과 형식을 확인한다.
6. 못 찾은 번호가 있으면 번호 형식(국가 코드, 종류 코드, 앞자리 0)을 PATSTAT의 형식에 맞춘다. 미국 공보번호에 종류 코드가 붙어 있는지 확인해 publn_kind 열과 맞춘다.
7. 전체를 실행하고, 고유 docdb_family_id 수가 번호 목록의 행 수에 가까운지, 고유 inpadoc_family_id 수가 그보다 적은지 본다.
8. 결과를 CSV로 저장해 대화형 AI에 업로드하면 단순 패밀리와 확장 패밀리에서 묶음이 달라지는 패밀리의 표를 만들게 할 수 있다.

**라. PATSTAT 없이 SQL 확인하기**

9. PATSTAT을 쓸 수 없으면 대화형 AI에게 "TLS201_APPLN, TLS204_APPLN_PRIOR, TLS211_PAT_PUBLN과 같은 열을 가진 작은 모의 테이블(출원 8건, 그중 둘은 우선권을 공유)을 만들어 이 SQL을 실행하고 결과를 보여 줘"라고 요청한다. 결합과 집계가 뜻대로 되는지는 이것으로 확인할 수 있다. 책의 예제 B.2의 표를 모의 테이블로 써도 된다.

이 절차는 2026년 11월에 확인한 것이다. 화면 이름, 한도, 요금은 바뀔 수 있으니 다르면 서비스의 공식 안내를 따른다.

**준비**

- ① appB 폴더의 families.csv(family_id, rep_pub_number 두 열만 쓴다)와 PATSTAT 데이터 카탈로그(PDF)의 TLS201_APPLN·TLS204_APPLN_PRIOR·TLS211_PAT_PUBLN 열 정의를 복사한 텍스트를 업로드하고 AI에게 번호 목록의 행 수를 묻는다.
- ② 실습 번호는 가상 번호라 PATSTAT에 없으니, 받은 SQL은 자기 번호 목록(같은 두 열)으로 실행하고 미국 공보번호에 종류 코드가 붙어 있는지 확인해 publn_kind 열과 형식을 맞춘다.

**먼저 보낼 말**

```text
첨부한 families.csv의 행 수를 알려 줘.
```

**프롬프트**

```text
PATSTAT Global의 TLS201_APPLN과 TLS211_PAT_PUBLN으로, 첨부한 families.csv의 대표 문서 번호(rep_pub_number)에 해당하는 appln_id를 찾는 SQL을 써 줘. 공보 번호는 publn_auth, publn_nr, publn_kind로 나누어 맞추고, 찾지 못한 번호의 목록도 출력해 줘.
찾은 출원에 docdb_family_id와 inpadoc_family_id를 붙여, families.csv의 family_id와 docdb_family_id가 일치하는 비율, 고유 docdb_family_id 수, 고유 inpadoc_family_id 수, 확장 패밀리 하나에 단순 패밀리가 몇 개씩 들어가는지의 분포를 표로 정리해 줘.
단순 패밀리가 셋 이상 묶인 확장 패밀리 10개를 뽑아 TLS204_APPLN_PRIOR로 우선권 구조를 정리하고, 어떤 경우에 묶였는지(여러 우선권의 결합, 일부 계속 출원 등) 설명해 줘. 실행한 PATSTAT의 판(봄판·가을판)을 결과에 적어 줘.
```
