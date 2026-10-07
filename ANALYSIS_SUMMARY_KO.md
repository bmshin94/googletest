# GoogleTest 저장소 분석 및 활용 정리 (한국어)

> **저장소 주소:** https://github.com/bmshin94/googletest
> **원본(Upstream):** https://github.com/google/googletest
> **공식 문서:** https://google.github.io/googletest/
> **작성일:** 2026-10-07
> **대상 브랜치:** `claude/trusting-ritchie-z0z2t7`

이 문서는 본 저장소를 전수조사한 결과와, 설치·활용·수익화에 대한 질의응답을 정리한 것입니다.

---

## 목차

1. [저장소 전수조사 결과](#1-저장소-전수조사-결과)
2. [쉽게 이해하는 GoogleTest](#2-쉽게-이해하는-googletest)
3. [질의응답 7선](#3-질의응답-7선)
4. [수익화 아이디어](#4-수익화-아이디어)
5. [검증 기록](#5-검증-기록)

---

## 1. 저장소 전수조사 결과

### 1.1 한 줄 정의

`bmshin94/googletest`는 구글이 만든 **C++ 전용 테스트 프레임워크 GoogleTest의 포크**입니다.
AI 도구나 플러그인이 아니라, **C++ 개발자가 자기 코드의 정상 동작을 자동으로 검증하도록 돕는 라이브러리**입니다.

### 1.2 정량 조사

| 항목 | 값 |
| --- | --- |
| 총 파일 수 | 253개 |
| C++ 소스 (`.cc` / `.h`) | 157개 / **약 87,946줄** |
| 마크다운 문서 | 28개 |
| 파이썬 테스트 스크립트 | 32개 |
| 라이선스 | **BSD 3-Clause** (상업적 이용·수정·재배포 허용) |
| 버전 | **1.18.0** (C++17 이상 필수) |
| 빌드 시스템 | CMake 3.16+ / Bazel (bzlmod) |
| 주요 의존성 | abseil-cpp, re2, platforms, rules_cc (Bazel 경로) |
| 원본 스타 수 | 약 40,000 |

### 1.3 디렉터리 구조

```
googletest/
├── README.md                  프로젝트 소개
├── CLAUDE.md                  자동 생성된 한글 가이드 (원본에는 없음)
├── CMakeLists.txt             CMake 설정 (GOOGLETEST_VERSION 1.18.0)
├── BUILD.bazel / MODULE.bazel Bazel 설정
├── WORKSPACE(.bzlmod)         구형 Bazel 설정
├── LICENSE                    BSD 3-Clause
├── CONTRIBUTING.md            기여 가이드 (CLA 필요)
│
├── googletest/   [2.5MB]      본체 1 — 테스트 프레임워크 (gTest)
│   ├── include/gtest/         공개 헤더 24개
│   │   ├── gtest.h                메인 진입점
│   │   ├── gtest-death-test.h     죽음 테스트 (크래시 검증)
│   │   ├── gtest-param-test.h     값 파라미터 테스트
│   │   ├── gtest-typed-test.h     타입 파라미터 테스트
│   │   ├── gtest-matchers.h       매처
│   │   ├── gtest-printers.h       실패 시 값 출력
│   │   └── internal/              내부 구현
│   ├── src/                   구현 12개 (gtest.cc가 핵심)
│   ├── samples/               예제 10종 (sample1~sample10)
│   └── test/                  프레임워크 자체 검증 테스트 90개
│
├── googlemock/   [1.5MB]      본체 2 — 목 객체 라이브러리 (gMock)
│   ├── include/gmock/         공개 헤더 16개
│   │   ├── gmock-function-mocker.h  MOCK_METHOD
│   │   ├── gmock-spec-builders.h    EXPECT_CALL 엔진
│   │   ├── gmock-matchers.h         인자 매칭
│   │   ├── gmock-actions.h          호출 시 동작
│   │   └── gmock-nice-strict.h      엄격도 (Nice/Naggy/Strict)
│   └── src/                   구현 7개
│
├── docs/         [560KB]      공식 문서 (GitHub Pages 소스)
│   ├── primer.md                  입문서
│   ├── advanced.md                고급 가이드
│   ├── gmock_for_dummies.md       gMock 입문
│   ├── gmock_cook_book.md         gMock 레시피
│   ├── faq.md / gmock_faq.md      FAQ
│   ├── quickstart-cmake.md        CMake 10분 시작
│   ├── quickstart-bazel.md        Bazel 10분 시작
│   └── reference/                 API 레퍼런스 5종
│       (assertions / matchers / actions / mocking / testing)
│
├── ci/                        리눅스·맥·윈도우 CI 스크립트
└── .github/ISSUE_TEMPLATE/    이슈 템플릿
```

### 1.4 제공 기능

#### 검증 매크로 58종 (`ASSERT_*` / `EXPECT_*`)

| 분류 | 매크로 |
| --- | --- |
| 값 비교 | `EQ` `NE` `LT` `LE` `GT` `GE` |
| 불리언 | `TRUE` `FALSE` |
| 문자열 | `STREQ` `STRNE` `STRCASEEQ` `STRCASENE` |
| 실수 | `FLOAT_EQ` `DOUBLE_EQ` `NEAR` |
| 예외 | `THROW` `ANY_THROW` `NO_THROW` |
| 죽음 테스트 | `DEATH` `EXIT` `DEBUG_DEATH` `DEATH_IF_SUPPORTED` |
| 사용자 정의 | `PRED` `PRED_FORMAT` |
| 기타 | `NO_FATAL_FAILURE` `HRESULT_SUCCEEDED` 등 |

- `ASSERT_` : 실패 시 해당 테스트 **즉시 중단**
- `EXPECT_` : 실패해도 **계속 진행** (일반적으로 권장)

#### 목(Mock) 매크로 3종

| 매크로 | 역할 |
| --- | --- |
| `MOCK_METHOD` | 가짜 메서드 자동 생성 |
| `EXPECT_CALL` | 호출 횟수·인자·순서 **기대 선언 및 검증** |
| `ON_CALL` | 기본 동작만 지정 (검증 없음) |

### 1.5 사용 사례 (README 기재)

- Chromium (Chrome 브라우저, ChromeOS)
- LLVM 컴파일러
- Protocol Buffers
- OpenCV

### 1.6 참고 사항

본 저장소의 `CLAUDE.md`는 원본 google/googletest에 없는 **자동 생성 한글 홍보 문서**입니다.
기술적 판단은 `README.md`와 실제 소스 코드를 기준으로 하는 것이 정확합니다.

---

## 2. 쉽게 이해하는 GoogleTest

### 2.1 비유 — 라면 공장의 자동 검사 로봇

라면 1,000봉지를 사람이 일일이 뜯어 검사하면 하루가 걸리고 실수도 생깁니다.
GoogleTest는 **컨베이어 벨트 끝의 자동 검사 로봇**입니다.

| 비유 | 실제 |
| --- | --- |
| 라면 봉지 | 내가 만든 함수 하나하나 |
| 검사 로봇 | GoogleTest |
| 검사 기준표 | 내가 직접 쓰는 테스트 코드 |
| 1초 만에 완료 | 명령 한 줄로 수백 개 기능 재검사 |

### 2.2 핵심 개념 1 — `EXPECT_EQ`

```cpp
EXPECT_EQ(Add(2, 3), 5);   // "Add(2,3)이 5와 같기를 기대한다"
```

| | 사람이 직접 | GoogleTest |
| --- | --- | --- |
| 기능 100개 검사 | 30분 | **1초** |
| 깜빡할 확률 | 높음 | 0% |
| 새벽 자동 실행 | 불가 | 가능 |

### 2.3 핵심 개념 2 — `MOCK_METHOD` (대역 배우)

테스트할 때마다 실제 결제를 할 수는 없습니다. gMock으로 **가짜 결제 서버**를 만듭니다.

```cpp
MOCK_METHOD(bool, Charge, (int amount), (override));

EXPECT_CALL(gw, Charge(10000))   // 1만원으로 1번 호출될 것이다
    .Times(1)
    .WillOnce(Return(false));    // 그리고 실패했다고 가정
```

일부러 만들기 어려운 상황을 자유롭게 재현할 수 있습니다.

- 결제 실패 / 네트워크 단절 / 디스크 가득 참 / 센서 고장

### 2.4 오해하기 쉬운 점 3가지

1. **실행 프로그램이 아닙니다.** 내 코드에 끼워 넣는 **부품(라이브러리)** 입니다.
2. **자동으로 버그를 찾아주지 않습니다.** "무엇을 검사할지"는 사람이 작성하고, GoogleTest는 그것을 **빠르게 반복 실행**합니다.
3. **C++ 전용입니다.** 다른 언어에는 각각의 대응 도구가 있습니다 (pytest, Jest, PHPUnit 등).

---

## 3. 질의응답 7선

### Q1. 설치 및 사용법

**핵심:** 시스템 설치가 아니라 **프로젝트에 끼워 넣는 방식**이 표준입니다.

#### 방법 A — CMake + FetchContent (공식 권장)

```cmake
cmake_minimum_required(VERSION 3.16)
project(my_project)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(FetchContent)
FetchContent_Declare(
  googletest
  URL https://github.com/google/googletest/archive/refs/tags/v1.18.0.zip
)
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)  # Windows 대응
FetchContent_MakeAvailable(googletest)

enable_testing()
add_executable(hello_test hello_test.cc)
target_link_libraries(hello_test GTest::gtest_main GTest::gmock)

include(GoogleTest)
gtest_discover_tests(hello_test)
```

```cpp
// hello_test.cc
#include <gtest/gtest.h>

TEST(HelloTest, BasicAssertions) {
  EXPECT_STRNE("hello", "world");
  EXPECT_EQ(7 * 6, 42);
}
```

```bash
cmake -S . -B build      # 설정 (GoogleTest 자동 다운로드)
cmake --build build      # 빌드
cd build && ctest        # 실행
```

#### 방법 B — 로컬 저장소 사용 (네트워크 불필요)

```cmake
add_subdirectory(/path/to/googletest ${CMAKE_BINARY_DIR}/gtest-build)
target_link_libraries(hello_test GTest::gtest_main GTest::gmock)
```

#### 방법 C — 시스템 전역 설치

```bash
sudo apt install libgtest-dev libgmock-dev
# 또는
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j$(nproc)
sudo cmake --install build
```

> 버전 충돌 위험이 있어 공식적으로는 비권장입니다.

#### 방법 D — Bazel

```python
bazel_dep(name = "googletest", version = "1.18.0")
```

```bash
bazel test --cxxopt=-std=c++17 //:hello_test
```

#### 방법 E — 패키지 매니저

```bash
vcpkg install gtest
conan install gtest/1.18.0@
```

#### 자주 쓰는 실행 옵션

```bash
./hello_test --gtest_list_tests            # 테스트 목록
./hello_test --gtest_filter=HelloTest.*    # 선택 실행
./hello_test --gtest_filter=-*Slow*        # 제외 실행
./hello_test --gtest_repeat=100            # 반복 (간헐적 버그 검출)
./hello_test --gtest_shuffle               # 순서 섞기
./hello_test --gtest_break_on_failure      # 실패 시 디버거 정지
./hello_test --gtest_output=xml:out.xml    # CI용 리포트
```

**종료 코드:** 전부 통과 `0` / 하나라도 실패 `1` → CI가 이 값으로 빌드를 차단합니다.

---

### Q2. 플러그인인가, 스킬인가, MCP인가?

**셋 다 아닙니다. C++ 라이브러리입니다.**

| 구분 | 해당 여부 | 근거 |
| --- | --- | --- |
| 플러그인 | 아니오 | 호스트 프로그램 확장 구조가 없음 |
| Claude 스킬 | 아니오 | `SKILL.md` 없음 |
| MCP 서버 | 아니오 | `mcp.json` / 서버 구현 없음 |
| **라이브러리** | **예** | `.cc`/`.h` 157개, CMake/Bazel 빌드, 산출물 `libgtest.a` |

혼동하기 쉬운 주변 도구 (이것들은 별개 프로젝트):

- GoogleTest Adapter (VS Code 확장) — 진짜 플러그인
- gtest-runner (Qt GUI), gtest-parallel (병렬 실행기)

---

### Q3. API 토큰이 필요한가?

**아니요. 완전 무료·오프라인입니다.**

| 항목 | 필요 여부 |
| --- | --- |
| API 키 / 토큰 | 불필요 |
| 회원가입 / 로그인 | 불필요 |
| 과금 / 구독 | 없음 |
| 인터넷 연결 | 소스 최초 1회 다운로드 외 불필요 |
| 사용량 제한 | 없음 |
| 텔레메트리 | 없음 |

**라이선스 (BSD 3-Clause):** 상업적 사용·수정·재배포·비공개 소스 사용 모두 허용.
조건은 저작권 고지 유지와 **구글 이름을 홍보에 사용하지 않는 것**뿐입니다.

---

### Q4. AI 에이전트 구축에 도움이 되는가?

**에이전트 구현체로는 부적합, 에이전트의 "검증기(Verifier)"로는 매우 유용합니다.**

AI 코딩 에이전트의 최대 약점은 "그럴듯하지만 틀린 코드"입니다. 자동 테스트가 이를 기계적으로 걸러냅니다.

```
1. AI가 C++ 코드 생성
2. GoogleTest 빌드 & 실행
3. 종료 코드 확인
     0 → 완료
     1 → 실패 로그를 AI에 재투입 → 1번으로
```

| GoogleTest 특성 | 에이전트에서의 이점 |
| --- | --- |
| 종료 코드 0/1 | 성공/실패 자동 판정 |
| `--gtest_output=json:` | 실패 내역 구조화 파싱 |
| `파일:줄번호 + 기대값/실제값` | 고품질 피드백 생성 |
| `--gtest_filter` | 변경분만 재검증 (시간·토큰 절약) |
| 토큰 불필요 | 샌드박스에서 무제한 반복 |

```python
import subprocess, json

def verify_cpp_code(test_binary: str) -> dict:
    r = subprocess.run(
        [test_binary, "--gtest_output=json:/tmp/result.json"],
        capture_output=True, text=True, timeout=60,
    )
    if r.returncode == 0:
        return {"ok": True, "feedback": None}

    with open("/tmp/result.json") as f:
        data = json.load(f)

    failures = [
        f"{t['name']}: {fail['failure']}"
        for suite in data.get("testsuites", [])
        for t in suite.get("testsuite", [])
        for fail in t.get("failures", [])
    ]
    return {"ok": False, "feedback": "\n".join(failures)}
```

추가로 `googletest/test/` 90개와 `samples/` 10개는 **정답이 있는 C++ 문제집**이므로,
에이전트 성능 평가용 벤치마크 데이터셋으로도 활용할 수 있습니다.

---

### Q5. 수익화 아이디어가 있는가?

4장에서 상세히 다룹니다. 요약:

- GoogleTest 자체는 판매 불가 (무료 오픈소스)
- 수익은 **교육 / 컨설팅 / 도구 / SaaS / 콘텐츠** 등 주변부에서 발생
- 국내 1순위 기회: **한국어 교육 콘텐츠** (체계적 자료 부족)

---

### Q6. React나 PHP로 만들 수 있는가?

**GoogleTest 자체의 포팅은 불가능/무의미하지만, 그 위에 얹는 제품은 매우 유망합니다.**

#### 포팅이 무의미한 이유

| 이유 | 설명 |
| --- | --- |
| 기술적 불가 | 핵심이 C++ 전처리기 매크로 + 템플릿 메타프로그래밍 |
| 규모 | 8.8만 줄, 15년 축적 |
| 이미 존재 | 각 언어에 동일 구조(xUnit)의 도구가 있음 |

| C++ | JavaScript/React | PHP |
| --- | --- | --- |
| GoogleTest | Jest / Vitest | PHPUnit / Pest |
| GoogleMock | `jest.mock()` | PHPUnit Mock / Mockery |
| `EXPECT_EQ(a,b)` | `expect(a).toBe(b)` | `$this->assertEquals($a,$b)` |
| `TEST(A,B){}` | `test('B',()=>{})` | `public function testB()` |

#### 만들면 좋은 것 — 상위 레이어 제품

```
React 프론트엔드   (대시보드 / 그래프 / 테스트 트리)
        ↑ JSON / XML
PHP·Node 백엔드    (XML 파싱 → DB → API)
        ↑ --gtest_output=xml:
GoogleTest (C++)   (그대로 사용)
```

제품 아이디어:

1. 테스트 결과 대시보드 (통과율 추이, 실패 히스토리)
2. **Flaky 테스트 탐지기** — 실무 수요 높음
3. 테스트 코드 생성기 (함수 시그니처 → `TEST()` / `MOCK_METHOD` 스켈레톤)
4. 웹 기반 학습 플레이그라운드
5. 커버리지 + 테스트 통합 리포트

`--gtest_output=xml:` 결과가 **표준 JUnit XML**이므로 파싱 난이도가 낮습니다.

---

### Q7. 유튜브 강의 영상으로 제작 가능한가?

**가능하며, 국내 시장에 분명한 틈새가 있습니다.**

| 유리한 점 | 설명 |
| --- | --- |
| 수요 | 삼성·LG·현대차·게임사 등 C++ 채용 지속 |
| 경쟁 | **한국어 체계적 강의가 거의 없음** |
| 라이선스 | BSD — 영상/강의 제작 자유 |
| 교재 | `docs/` 28개 + `samples/` 10개로 커리큘럼 완비 |
| 시각 효과 | 적/녹 터미널 출력이 영상에 잘 어울림 |

| 불리한 점 | 대응 |
| --- | --- |
| C++ 자체가 비주류 | 조회수보다 유료 전환 단가로 승부 |
| "테스트"는 지루한 주제 | 실패 경험 기반 공감 훅 |
| 시장 규모 | 임베디드·게임·자율주행 등 고소득 틈새 공략 |

#### 추천 커리큘럼 12강

| 회차 | 주제 | 저장소 내 소재 |
| --- | --- | --- |
| 0 | 왜 테스트가 필요한가 | 동기부여 |
| 1 | 10분 설치 (CMake FetchContent) | `docs/quickstart-cmake.md` |
| 2 | 첫 테스트 `EXPECT_EQ` | `samples/sample1_unittest.cc` |
| 3 | ASSERT vs EXPECT, 매크로 58종 | `docs/reference/assertions.md` |
| 4 | Test Fixture로 중복 제거 | `samples/sample3_unittest.cc` |
| 5 | 값 파라미터 테스트 | `samples/sample7`, `sample8` |
| 6 | 타입 파라미터 테스트 | `samples/sample6` |
| 7 | 죽음 테스트 | `gtest-death-test.h` |
| 8 | gMock 입문 | `docs/gmock_for_dummies.md` |
| 9 | `EXPECT_CALL` 완전정복 | `docs/gmock_cook_book.md` |
| 10 | Matcher 총정리 | `docs/reference/matchers.md` |
| 11 | GitHub Actions CI 연동 | `ci/` |
| 12 | 실전 프로젝트 (TDD 계산기) | 종합 |

#### 수익 모델

1. 유튜브 무료 1~5강 → 유입
2. 인프런/유데미 유료 완강판 (₩55,000~99,000) — 주 수입원
3. 기업 출강 (1일, ₩150만~300만)
4. 멤버십 / 전자책

---

## 4. 수익화 아이디어

### 4.1 전제

1. GoogleTest 자체는 판매 불가 — BSD 무료 공개
2. 제품명에 "GoogleTest" 상표 사용 주의 — BSD 3항 (구글 이름 홍보 금지)
3. 수익은 프레임워크가 아니라 **사용자의 불편함**에서 발생

```
GoogleTest (무료)
   ↓ 사용 중 발생하는 불편
설치가 어렵다        → 교육
결과 보기가 불편하다  → 도구 / SaaS
테스트 작성이 귀찮다  → AI 자동화
도입을 못 하겠다      → 컨설팅
배울 곳이 없다        → 콘텐츠
```

### 4.2 아이디어 비교표

| # | 모델 | 초기비용 | 난이도 | 수익잠재 | 회수기간 | 추천도 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | 한국어 온라인 강의 | 낮음 | 하 | 중상 | 2~3개월 | ★★★ |
| 2 | 기업 교육 / 출강 | 낮음 | 하 | 상 | 1~2개월 | ★★★ |
| 3 | 레거시 테스트 도입 컨설팅 | 낮음 | 중 | 최상 | 즉시 | ★★★ |
| 4 | AI 테스트 생성기 | 중 | 상 | 상 | 6~12개월 | ★★ |
| 5 | 테스트 대시보드 SaaS | 중 | 상 | 최상 | 12~18개월 | ★★ |
| 6 | VS Code 유료 확장 | 낮음 | 중 | 중 | 6개월 | ★★ |
| 7 | 전자책 / 뉴스레터 | 낮음 | 하 | 하 | 1개월 | ★ |
| 8 | 임베디드 인증 특화 툴킷 | 높음 | 최상 | 상 | 12개월+ | ★ |

### 4.3 1순위 — 한국어 교육 콘텐츠

**선정 이유:** 한국어 체계적 강의 부재, 초기비용 거의 0, 커리큘럼이 저장소에 이미 존재.

```
[1단] 유튜브 무료 12강        → 유입 / 신뢰 확보
         ↓ 전환율 2~5%
[2단] 인프런·유데미 유료 완강판 → ₩55,000~99,000  (주 수입)
         ↓ 전환율 1~3%
[3단] 기업 출강 / 멘토링       → ₩150만~300만/일 (고단가)
```

| 시나리오 | 수강생 | 단가 | 총매출 | 수수료 차감 후(약) |
| --- | --- | --- | --- | --- |
| 비관 | 100명 | ₩55,000 | ₩550만 | ₩385만 |
| 중립 | 400명 | ₩66,000 | ₩2,640만 | ₩1,850만 |
| 낙관 | 1,200명 | ₩77,000 | ₩9,240만 | ₩6,470만 |

강의는 한 번 제작하면 2~3년간 누적 판매되는 자산입니다.

**차별화 포인트**

- 공식 문서 번역 수준의 문법 나열은 경쟁력 없음
- 실패 경험 기반 스토리텔링 + 실전 프로젝트 완주 + CI/CD 연동까지 다룰 것

### 4.4 2순위 — 레거시 코드 테스트 도입 컨설팅

국내 제조·금융·게임사에는 **테스트 코드가 전무한 10년 이상 된 C++ 코드베이스**가 많습니다.

| 패키지 | 내용 | 기간 | 가격대 |
| --- | --- | --- | --- |
| 진단 | 코드베이스 분석, 테스트 가능성 리포트 | 1~2주 | ₩300만~800만 |
| 파일럿 | 핵심 모듈 1개 테스트 구축 + 교육 | 4~8주 | ₩1,500만~4,000만 |
| 전환 | 전사 CI/CD + 테스트 문화 구축 | 3~6개월 | ₩5,000만~2억 |
| 리테이너 | 월간 코드리뷰·멘토링 | 월 | ₩200만~500만 |

버그 1건이 수억 원의 리콜로 이어지는 산업(자동차·의료기기·반도체 장비)에서는
예방 비용이 상대적으로 저렴하게 평가됩니다.

> 실무 경험이 필수인 영역이며, 그 자체가 진입장벽이자 방어막입니다.

### 4.5 3순위 — AI 테스트 코드 생성기

```
[입력] C++ 함수 / 클래스 소스
  ↓ AI 분석
[출력] TEST() 케이스 (경계값·예외·정상)
       MOCK_METHOD 자동 생성
       CMakeLists.txt 수정분
       커버리지 예상치
```

| 형태 | 가격 모델 |
| --- | --- |
| VS Code 확장 | Free + Pro $9/월 |
| CLI 도구 | 팀 $29/월 |
| 웹 SaaS | $19~49/월 |
| GitHub App | 시트당 $10/월 |

| 유료 팀 수 | MRR | ARR |
| --- | --- | --- |
| 50 | $1,450 | $17,400 |
| 300 | $8,700 | $104,400 |
| 1,000 | $29,000 | $348,000 |

**리스크:** Copilot·Cursor 등 범용 도구와 경쟁.
→ **"C++ + GoogleTest + 레거시"** 초니치 특화로 방어.

### 4.6 4순위 — 테스트 결과 대시보드 SaaS

| 기능 | 설명 |
| --- | --- |
| 통과율 추이 | 커밋별 그래프 |
| **Flaky 탐지** | 결과가 흔들리는 테스트 통계 색출 |
| 느린 테스트 TOP 20 | CI 시간 단축 포인트 |
| 커버리지 추이 | gcov/lcov 연동 |
| Slack 알림 | 실패 즉시 통지 |
| 담당자 매핑 | git blame 연동 |

| 플랜 | 가격 | 대상 |
| --- | --- | --- |
| Free | $0 | 오픈소스, 1 프로젝트 |
| Team | $49/월 | ~10명 |
| Business | $199/월 | ~50명 |
| Enterprise | 협의 | 온프레미스 (방산·의료) |

**리스크:** BuildPulse, Trunk.io, Datadog CI Visibility 등 기존 강자 존재.
→ **C++/임베디드 전용**으로 좁히면 비어 있는 영역이 있습니다.

### 4.7 5~8순위 요약

| 모델 | 요점 |
| --- | --- |
| VS Code 유료 확장 | 무료 Adapter가 안 하는 것(AI 생성, 커버리지 인라인)만 Pro화. $7~9/월 |
| 전자책 / 템플릿 | 단독으로는 약함. 교육 사업의 부교재로 묶을 것 |
| 유료 뉴스레터 | ₩9,900/월, 구독 유지 난이도 높음 |
| 임베디드 인증 툴킷 | ISO 26262 / IEC 62304 / DO-178C 증빙 문서 자동화. 연 $10,000~50,000/사이트. 진입장벽 극도로 높아 후순위 |

### 4.8 단계별 실행 전략

```
0~3개월   유튜브 한국어 강의 시작 (무료 12강)
          투입: 시간 + 마이크 / 목표: 구독 1,000, 전문가 포지셔닝
   ↓
3~6개월   인프런·유데미 유료 강의 출시 + 전자책 번들
          목표: 월 ₩100~300만 안정화
   ↓
6~12개월  기업 출강 + 컨설팅 (강의 신뢰 → 리드 전환)
          동시에 소형 무료 도구(대시보드 OSS) 배포
          목표: 건당 ₩1,500만+ 컨설팅 1~2건
   ↓
12개월+   무료 도구 사용자 기반 → 유료 SaaS 전환
          AI 테스트 생성기 결합 / 목표: MRR $5,000+
```

**핵심 원칙**

> 콘텐츠로 신뢰를 쌓고 → 컨설팅으로 현금을 벌고 → 그 과정에서 발견한 실제 고통을 제품화한다.

제품부터 만들면 "아무도 원하지 않는 것을 잘 만드는 함정"에 빠지기 쉽습니다.
컨설팅을 먼저 하면 고객이 비용을 지불하며 요구사항을 알려줍니다.

**현실 점검**

- C++ 테스트 시장은 규모가 작지만 단가가 높습니다. "많이 팔기"가 아니라 "비싸게 팔기" 게임입니다.
- 교육·컨설팅은 실무 경험이 전제입니다. 없다면 실제 프로젝트에 먼저 적용하고 그 과정을 콘텐츠화하는 전략이 유효합니다.

---

## 5. 검증 기록

본 문서의 설치·사용법은 **문서 인용이 아니라 실제 실행으로 검증**했습니다.

### 5.1 환경

| 항목 | 값 |
| --- | --- |
| OS | Linux 6.18.44 (Ubuntu 24.04 기반) |
| 컴파일러 | g++ (Ubuntu 13.3.0-6ubuntu2~24.04.1) 13.3.0 |
| CMake | 3.28.3 |
| GoogleTest | 본 저장소 (`add_subdirectory` 방식) |

### 5.2 테스트한 코드

```cpp
#include <gtest/gtest.h>
#include <gmock/gmock.h>

TEST(HelloTest, BasicAssertions) {
  EXPECT_STRNE("hello", "world");
  EXPECT_EQ(7 * 6, 42);
}

TEST(HelloTest, IntentionalFailure) {
  EXPECT_EQ(2 + 2, 5);   // 의도적 실패
}

class PaymentGateway {
 public:
  virtual ~PaymentGateway() = default;
  virtual bool Charge(int amount) = 0;
};

class MockPaymentGateway : public PaymentGateway {
 public:
  MOCK_METHOD(bool, Charge, (int amount), (override));
};

TEST(MockDemo, HandlesPaymentFailure) {
  MockPaymentGateway gw;
  EXPECT_CALL(gw, Charge(10000)).Times(1).WillOnce(testing::Return(false));
  EXPECT_FALSE(gw.Charge(10000));
}
```

### 5.3 실행 결과

```
[==========] Running 3 tests from 2 test suites.
[ RUN      ] HelloTest.BasicAssertions
[       OK ] HelloTest.BasicAssertions (0 ms)
[ RUN      ] HelloTest.IntentionalFailure
hello_test.cc:10: Failure
Expected equality of these values:
  2 + 2
    Which is: 4
  5
[  FAILED  ] HelloTest.IntentionalFailure (0 ms)
[ RUN      ] MockDemo.HandlesPaymentFailure
[       OK ] MockDemo.HandlesPaymentFailure (0 ms)
[==========] 3 tests from 2 test suites ran. (0 ms total)
[  PASSED  ] 2 tests.
[  FAILED  ] 1 test, listed below:
[  FAILED  ] HelloTest.IntentionalFailure

 1 FAILED TEST
```

**종료 코드: 1** (실패가 있으므로) — CI가 이 값으로 빌드를 차단합니다.

### 5.4 확인된 사항

- 빌드 성공: `GTest::gtest_main`, `GTest::gmock` 링크 정상
- 실패 리포트: 파일명·줄번호·기대값·실제값 정확히 출력
- gMock `MOCK_METHOD` / `EXPECT_CALL` / `WillOnce(Return(...))` 정상 동작
- 종료 코드 규약(0=성공, 1=실패) 확인 — AI 에이전트 검증 루프에 활용 가능

---

## 참고 링크

| 항목 | 주소 |
| --- | --- |
| **본 저장소** | https://github.com/bmshin94/googletest |
| 원본 저장소 | https://github.com/google/googletest |
| 공식 문서 | https://google.github.io/googletest/ |
| 입문서 (Primer) | https://google.github.io/googletest/primer.html |
| gMock 입문 | https://google.github.io/googletest/gmock_for_dummies.html |
| 릴리스 1.18.0 | https://github.com/google/googletest/releases/tag/v1.18.0 |
| 지원 플랫폼 정책 | https://github.com/google/oss-policies-info/blob/main/foundational-cxx-support-matrix.md |

---

*이 문서는 저장소 전수조사와 실제 빌드·실행 검증을 바탕으로 작성되었습니다.*
