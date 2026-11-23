# appB — 국가별 상태 테이블 조립과 패밀리 대조

자료 기준일 2025-06-30. 이 데이터셋의 모든 기업·기관·특허·번호·인물·텍스트는 교육용으로 만든 가상의 것이며 실제 특허가 아니다.

이 폴더의 파일만으로 부록 B의 프롬프트와 소과제를 할 수 있다. 다른 폴더의 파일은 필요 없다.

- prompts.md: 부록 B의 프롬프트 2개. 준비, 먼저 보낼 말, 복사해 보낼 프롬프트를 차례로 적었다. 준비와 프롬프트는 책에 실린 글과 같다. 모두 대화형 AI에 이 폴더의 파일을 업로드해 따라 할 수 있고, 외부 서비스가 필요하거나 알아 둘 점이 있는 프롬프트에는 "진행 방법"을 더했다.
- data_dictionary.csv: 이 폴더 파일의 열 정의(file, column, type, description_ko, example).

## 파일 표

| 파일 | 행 수 | 열 수 | 구분 |
|---|---:|---:|---|
| families.csv | 5,259 | 31 | 정제본 |
| family_publications.csv | 27,780 | 9 | 정제본 |
| kr_patents.csv | 3,512 | 22 | 정제본 |
| kr_annual_fees.csv | 7,141 | 7 | 정제본 |
| kr_admin_events.csv | 31,190 | 7 | 정제본 |
| kr_rights_events.csv | 41 | 5 | 정제본 |

부록 B의 프롬프트 B.1(국가별 상태 테이블 조립)과 B.2(PATSTAT으로 단순·확장 패밀리 대조)에 쓰는 파일이다.

안내
- 실습 번호는 가상 번호라 KIPRIS Plus, 유럽특허청 OPS, PATSTAT으로 조회되지 않는다. 프롬프트 B.1의 1단계(OPS 패밀리 조회)는 family_publications.csv로 대신하고 3단계(EP 법적 사건)는 건너뛴다.
- 기준일은 자료 기준일 2025-06-30이다. 한국 특허의 n년차분 납부 기한은 등록일 + (n−1)년, 추가납부 기간은 그 뒤 6개월, 회복 신청 기간은 그 뒤 3개월이다.
- kr_patents.csv에는 자사 출원 700건, 대학 312건, 분야 한국 DB 2,500건이 들어 있다. families.csv와는 family_id로 잇는다.

실습할 때 알아 둘 점

- 우선심사 등으로 출원공개 전에 등록공고가 된 한국 출원 196건은 kr_patents.csv의 open_no·open_date가 빈값이고, kr_admin_events.csv에 출원공개 사건이 없으며, family_publications.csv에 공개공보(kind가 A인 행)가 없다.
- family_publications.csv의 관청 수가 families.csv의 family_size보다 작은 패밀리가 100건 있다(우선 2022~2023년). 출원은 했지만 기준일까지 공보가 나오지 않은 관청이 있기 때문이다. 국가별 상태 테이블에서는 그 관청을 "공보 없음"으로 따로 적는다.

## 정제본에서 가져온 파일

아래 파일은 7장의 데이터 사전(책의 표 7.1)대로 만든 정제본이다. 같은 이름의 파일은 어느 폴더에서나 내용이 같다.

| 파일 | 내용 |
|---|---|
| families.csv | 패밀리 마스터. 한 행이 패밀리 한 건(정제본 5,199건 + 자사 과거 등록 특허 60건). 대표 문서, 날짜, 출원인, CPC, sub_tech, 인용 수, norm_cit(참고판), legal_status, 심사 이력, 발명자, 만료일, in_analysis_set |
| family_publications.csv | 패밀리×관청 문헌. US·EP·JP·CN·KR·WO 문헌의 번호·날짜·출원번호 |
| kr_patents.csv | 한국 출원(자사·대학·분야 DB). status_kr은 행정 상태(등록·소멸·거절·취하·포기·심사중) |
| kr_annual_fees.csv | 연차료 납부. 설정등록(1~3년분)과 연차별 납부, 추가납부 가산금 |
| kr_admin_events.csv | 행정 사건. KIPRIS 행정처리 형식의 서류명·일자 |
| kr_rights_events.csv | 권리 변동. 실시권·질권·이전·회복 |

families.csv를 쓸 때의 주의

- in_analysis_set이 1인 5,199건이 분석 대상인 정제본(미국 우선 연도 2014~2023년)이다. 0인 60건은 자사(솔베나르소재, SOLVENAR MATERIALS CO., LTD., assignee_id A0001)의 과거 미국 등록 특허(우선 2006~2013년)이며, 연도별 비교·점유율·네트워크에는 넣지 않는다.
- norm_cit는 참고판 값이다. 등록 후 5년 인용 창이 기준일 안에 끝난 등록 패밀리만 값이 있고, 분모는 같은 우선 연도·같은 세부 기술의 평균이다.
- legal_status(pending, abandoned, in_force, grace, lapsed, expired)는 반드시 status_as_of(2025-06-30)와 함께 읽는다.
- 미국 문서가 없는 패밀리 1건(비공개 요청, 심사 중)은 rep_pub_number가 비어 있다.
