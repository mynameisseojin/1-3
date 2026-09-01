ChatGPT의 말:
좋아요. 과제에서 요구하는 형식을 그대로 만족하도록, 실제로 구현하고 제출할 수 있는 형태로 프로젝트 1과 프로젝트 2를 통째로 구성해드릴게요.

다만 제가 사용자의 Make/Zapier 계정에 직접 로그인해서 워크플로우를 생성하거나 실행할 수는 없으므로, 아래 설계대로 직접 구현하고 필요한 화면만 캡처하면 제출 가능한 수준으로 작성하겠습니다.

프로젝트 1. 자동화 도구 비교 구현
1. 주제
Google Sheets에 새로운 신청 데이터가 들어오면 점수에 따라 합격/불합격을 분기하고, 각각 다른 시트에 기록한 뒤 Discord로 결과를 알리는 자동화

사용하는 도구
Make
Zapier
Google Sheets
Discord
두 자동화 도구에서 동일한 워크플로우를 구현합니다.

2. 원본 데이터 구성
먼저 Google Sheets에 다음과 같이 작성합니다.

이름	이메일	점수
김민수	minsu@example.com	90
이지은	jieun@example.com	75
박준호	junho@example.com	85
최서연	seoyeon@example.com	60

시트 이름은 신청자로 설정합니다.

그리고 결과를 저장할 시트를 2개 만듭니다.

합격자
이름	이메일	점수	처리일시

불합격자
이름	이메일	점수	처리일시

3. Make 구현
워크플로우
Google Sheets
새 행 감지
      ↓
   Router
   ↙     ↘
점수 ≥ 80   점수 < 80
   ↓          ↓
합격자 시트   불합격자 시트
   ↓          ↓
Discord 알림  Discord 알림

Trigger
Google Sheets → Watch New Rows

설정:

Spreadsheet: 위에서 만든 스프레드시트
Sheet: 신청자
Trigger: 새로운 행이 추가되었을 때
Router
Trigger 다음에 Router를 추가합니다.

분기 1 — 합격
조건:

점수 >= 80

분기 2 — 불합격
조건:

점수 < 80

합격 Action 1
Google Sheets → Add a Row

대상:

합격자

매핑:

이름 → 이름
이메일 → 이메일
점수 → 점수
현재 시간 → 처리일시

합격 Action 2
Discord → Send a Message

메시지:

🎉 합격 처리

이름: {{이름}}
점수: {{점수}}점
결과: 합격

불합격 Action 1
Google Sheets → Add a Row

대상:

불합격자

매핑:

이름 → 이름
이메일 → 이메일
점수 → 점수
현재 시간 → 처리일시

불합격 Action 2
Discord → Send a Message

메시지:

📢 불합격 처리

이름: {{이름}}
점수: {{점수}}점
결과: 불합격

4. Make 테스트
반드시 두 분기를 모두 실행해야 합니다.

테스트 1
새 행 추가:

이름: 김민수
이메일: minsu@example.com
점수: 90

결과:

Google Sheets
    ↓
Router
    ↓
점수 >= 80
    ↓
합격자 시트 기록
    ↓
Discord 합격 알림

테스트 2
새 행 추가:

이름: 이지은
이메일: jieun@example.com
점수: 75

결과:

Google Sheets
    ↓
Router
    ↓
점수 < 80
    ↓
불합격자 시트 기록
    ↓
Discord 불합격 알림

5. Make 캡처 목록
제출용으로 다음 화면을 캡처합니다.

캡처 1
Make 전체 시나리오 화면

Trigger → Router → 2개 분기 → 각각 2개 Action

이 화면이 가장 중요합니다.

캡처 2
합격 조건 설정 화면

점수 >= 80

캡처 3
불합격 조건 설정 화면

점수 < 80

캡처 4
Make 실행 기록

합격 경로가 실행된 화면

캡처 5
Make 실행 기록

불합격 경로가 실행된 화면

캡처 6
Google Sheets 결과

합격자/불합격자 시트에 데이터가 들어간 화면

캡처 7
Discord 결과

합격/불합격 메시지가 전송된 화면

6. Zapier 구현
Zapier에서는 같은 구조를 구현합니다.

Google Sheets
새 행 추가
      ↓
조건 확인
   ↙     ↘
합격       불합격
 ↓          ↓
합격자 시트  불합격자 시트
 ↓          ↓
Discord     Discord

Trigger
Google Sheets → New Spreadsheet Row

대상:

신청자

새로운 행이 추가되면 자동으로 실행되도록 설정합니다.

분기
Zapier에서 Paths를 사용할 수 있다면 Paths를 이용합니다.

Path A — 합격
조건:

점수 >= 80

Path B — 불합격
조건:

점수 < 80

Path A Action 1
Google Sheets에서 Create Spreadsheet Row

대상:

합격자

값:

이름
이메일
점수
처리일시

Path A Action 2
Discord 메시지 전송:

🎉 합격 처리

이름: {{이름}}
점수: {{점수}}점
결과: 합격

Path B Action 1
Google Sheets에서 Create Spreadsheet Row

대상:

불합격자

Path B Action 2
Discord 메시지:

📢 불합격 처리

이름: {{이름}}
점수: {{점수}}점
결과: 불합격

7. Zapier 테스트
합격 테스트
김민수 / minsu@example.com / 90

결과:

New Spreadsheet Row
        ↓
Path A
        ↓
합격자 시트
        ↓
Discord

불합격 테스트
이지은 / jieun@example.com / 75

결과:

New Spreadsheet Row
        ↓
Path B
        ↓
불합격자 시트
        ↓
Discord

8. 프로젝트 1 비교 분석 보고서
아래 내용을 그대로 Markdown 보고서에 넣으면 됩니다.

작성


노코드 자동화 도구 비교 분석 보고서
1. 프로젝트 개요
이번 프로젝트에서는 Google Sheets에 새로운 신청자 데이터가 추가되었을 때 점수를 기준으로 합격자와 불합격자를 자동으로 분류하고, 결과를 각각의 Google Sheets에 기록한 뒤 Discord로 알림을 전송하는 자동화 워크플로우를 구현하였다.

동일한 워크플로우를 Make와 Zapier에서 각각 구현하여 두 자동화 도구의 특징과 장단점을 비교하였다.

자동화 흐름
Google Sheets
     ↓
새로운 데이터 감지
     ↓
조건 분기
   ↙     ↘
점수 ≥ 80  점수 < 80
   ↓         ↓
합격자 시트  불합격자 시트
   ↓         ↓
Discord 알림 Discord 알림

2. Make 구현 과정
Make에서는 Google Sheets의 새로운 행을 감지하는 모듈을 Trigger로 사용하였다.

Trigger가 실행되면 Router를 통해 두 개의 경로로 분기하였다.

첫 번째 경로에서는 점수가 80점 이상인 데이터를 합격자로 판단하여 합격자 시트에 기록하고 Discord에 합격 알림을 전송하였다.

두 번째 경로에서는 점수가 80점 미만인 데이터를 불합격자로 판단하여 불합격자 시트에 기록하고 Discord에 불합격 알림을 전송하였다.

Make 구성
Google Sheets - Watch New Rows
             ↓
           Router
          ↙      ↘
   점수 >= 80    점수 < 80
       ↓            ↓
Google Sheets   Google Sheets
합격자 기록      불합격자 기록
       ↓            ↓
Discord         Discord
합격 알림        불합격 알림

3. Zapier 구현 과정
Zapier에서도 Google Sheets의 새로운 행을 감지하는 Trigger를 사용하였다.

Trigger 이후 조건에 따라 두 개의 Path로 데이터를 분류하였다.

점수가 80점 이상이면 합격 Path를 실행하여 합격자 시트에 기록하고 Discord로 알림을 보냈다.

점수가 80점 미만이면 불합격 Path를 실행하여 불합격자 시트에 기록하고 Discord로 알림을 보냈다.

Zapier 구성
Google Sheets - New Spreadsheet Row
             ↓
            Paths
          ↙       ↘
   점수 >= 80     점수 < 80
       ↓             ↓
합격자 시트       불합격자 시트
       ↓             ↓
Discord           Discord

4. 비교 분석
비교 항목	Make	Zapier
UI/UX	시각적인 노드 기반 구성으로 전체 흐름을 확인하기 좋음	단계별 리스트 형태라 기본적인 자동화 구성이 직관적임
설정 난이도	다양한 기능을 제공하는 만큼 처음에는 다소 복잡함	기본적인 자동화는 비교적 쉽게 설정할 수 있음
조건 분기	Router와 Filter를 이용해 복잡한 분기 구성 가능	Paths와 Filter 등을 이용해 조건별 흐름 구성 가능
워크플로우 시각화	전체 시나리오를 한 화면에서 확인하기 편리함	단계가 많아질 경우 전체 구조를 파악하는 데 상대적으로 불편할 수 있음
연동 서비스	다양한 서비스와 세부적인 자동화 설정을 지원	매우 다양한 앱을 지원하며 앱 연결 과정이 편리함
실행 기록	실행별 단계와 데이터 흐름을 확인하기 편리함	Task History에서 실행 결과를 확인할 수 있음
세부 제어	필터, Router, 데이터 처리 등을 세밀하게 제어하기 좋음	빠르게 자동화를 구성하는 데 강점이 있음
학습 난이도	초보자에게는 상대적으로 높은 편	초보자가 시작하기 상대적으로 쉬움

5. Make의 장점과 단점
장점
전체 워크플로우를 시각적으로 확인하기 쉽다.
Router와 Filter를 활용한 복잡한 조건 분기에 적합하다.
여러 단계의 데이터 처리 과정을 하나의 시나리오에서 세밀하게 구성할 수 있다.
실행 과정에서 각 모듈을 거친 데이터를 확인하기 편리하다.
단점
처음 사용하는 사람에게는 인터페이스가 다소 복잡하게 느껴질 수 있다.
기능이 다양하기 때문에 기본적인 자동화만 필요한 경우 학습해야 할 내용이 많을 수 있다.
6. Zapier의 장점과 단점
장점
기본적인 자동화 워크플로우를 비교적 빠르게 만들 수 있다.
앱을 연결하고 Trigger와 Action을 설정하는 과정이 직관적이다.
다양한 외부 서비스를 쉽게 연결할 수 있다.
간단한 업무 자동화를 처음 시작하는 사용자에게 적합하다.
단점
복잡한 워크플로우에서는 전체 흐름을 한눈에 파악하기 어려울 수 있다.
세부적인 데이터 처리나 복잡한 분기가 필요한 경우 설정이 복잡해질 수 있다.
사용량과 기능에 따라 무료 플랜의 제약을 고려해야 한다.
7. 상황별 적합성
Make는 여러 조건 분기와 데이터 처리 과정이 포함된 복잡한 자동화에 적합하다고 판단하였다. 특히 이번 프로젝트처럼 하나의 Trigger 이후 여러 경로로 데이터를 분류해야 하는 경우 시각적인 워크플로우 구조가 장점으로 작용하였다.

반면 Zapier는 간단한 Trigger와 Action을 빠르게 연결하여 자동화를 구축하고 싶은 경우 적합하다고 판단하였다. 자동화를 처음 접하는 사용자가 기본적인 업무 자동화를 시작하기에도 편리하다.

8. 최종 의견
두 도구 모두 코드를 직접 작성하지 않고 반복 업무를 자동화할 수 있다는 공통점이 있다.

Make는 시각적인 워크플로우와 세밀한 조건 분기에 강점이 있고, Zapier는 간단하고 빠른 자동화 구성에 강점이 있다고 판단하였다.

따라서 단순한 업무 자동화에는 Zapier를, 여러 조건과 분기가 포함된 복잡한 업무 자동화에는 Make를 우선적으로 고려할 것이다.

이번 구현을 통해 Trigger는 자동화를 시작시키는 이벤트이고, Action은 Trigger 이후 실제 작업을 수행하는 단계이며, Filter 또는 Router는 특정 조건에 따라 작업의 실행 경로를 결정하는 역할을 한다는 것을 확인할 수 있었다.

프로젝트 2. 자유 주제 자동화
이번에는 AI까지 연결하기 쉬운 주제로 구성하겠습니다.

주제
고객 문의 자동 분류 및 기록/알림
반복 업무:

고객 문의가 들어올 때마다 내용을 확인하고, 문의 유형을 판단한 후 Google Sheets에 기록하고 담당자에게 Discord로 알리는 작업

이것을 자동화합니다.

1. 선정 도구
Make

선정 이유
Google Sheets, Gmail/Webhook, Discord 등의 서비스를 연결하기 쉽습니다.
Router와 Filter를 이용해 문의 유형별로 다른 작업을 수행할 수 있습니다.
전체 자동화 과정을 시각적으로 확인할 수 있어 과제의 학습 목표인 Trigger, Action, 조건 분기를 설명하기 좋습니다.
향후 AI 요약/분류 Action을 추가하기에도 적합합니다.
2. 데이터 구성
Google Sheets에 다음 열을 만듭니다.

접수시간	문의내용	문의유형	처리상태	담당자

문의 유형은 다음 세 가지로 정합니다.

결제
배송
기타

3. 워크플로우
Webhook
  ↓
문의 데이터 수신
  ↓
Router
  ├── 결제
  │     ↓
  │  Google Sheets 기록
  │     ↓
  │  Discord 알림
  │
  ├── 배송
  │     ↓
  │  Google Sheets 기록
  │     ↓
  │  Discord 알림
  │
  └── 기타
        ↓
     Google Sheets 기록
        ↓
     Discord 알림

4. Trigger
Make에서 Webhooks → Custom Webhook을 생성합니다.

Webhook 이름:

customer_inquiry

Webhook으로 다음과 같은 데이터를 전달합니다.

{
  "name": "김민수",
  "email": "minsu@example.com",
  "message": "결제한 상품의 환불은 어떻게 하나요?"
}

실제 제출 시 API Key나 비밀번호 같은 민감정보는 넣지 않습니다.

5. 문의 유형 분기
Webhook 다음에 Router를 추가합니다.

Route 1
조건:

문의내용에 "결제" 또는 "환불" 포함

문의 유형:

결제

Route 2
조건:

문의내용에 "배송" 또는 "택배" 포함

문의 유형:

배송

Route 3
그 외 모든 문의

문의 유형:

기타

6. 각 분기의 Action
결제 분기
Action 1
Google Sheets → Add a Row

접수시간 → 현재 시간
문의내용 → Webhook message
문의유형 → 결제
처리상태 → 접수
담당자 → 결제 담당

Action 2
Discord:

💳 결제 문의 접수

고객: {{name}}
문의: {{message}}
담당자: 결제 담당
상태: 접수

배송 분기
Action 1
Google Sheets → Add a Row

접수시간 → 현재 시간
문의내용 → Webhook message
문의유형 → 배송
처리상태 → 접수
담당자 → 배송 담당

Action 2
Discord:

📦 배송 문의 접수

고객: {{name}}
문의: {{message}}
담당자: 배송 담당
상태: 접수

기타 분기
Action 1
Google Sheets → Add a Row

접수시간 → 현재 시간
문의내용 → Webhook message
문의유형 → 기타
처리상태 → 접수
담당자 → 고객지원 담당

Action 2
Discord:

📢 기타 문의 접수

고객: {{name}}
문의: {{message}}
담당자: 고객지원 담당
상태: 접수

7. 프로젝트 2 테스트
조건 분기가 있기 때문에 최소 3개 테스트를 실행하는 것을 권장합니다.

테스트 1 — 결제
고객: 김민수
문의:
"결제한 상품을 환불하고 싶습니다."

예상 결과:

Webhook
 ↓
결제 Route
 ↓
Google Sheets
 ↓
Discord

테스트 2 — 배송
고객: 이지은
문의:
"주문한 상품의 배송이 언제 도착하나요?"

예상 결과:

Webhook
 ↓
배송 Route
 ↓
Google Sheets
 ↓
Discord

테스트 3 — 기타
고객: 박준호
문의:
"회원정보를 변경하고 싶습니다."

예상 결과:

Webhook
 ↓
기타 Route
 ↓
Google Sheets
 ↓
Discord

이렇게 하면 모든 분기 경로가 실제로 최소 1회 실행되므로 과제의 조건 분기 테스트 요구사항을 충족할 수 있습니다.

8. 프로젝트 2 제출 문서
작성


프로젝트 2. 고객 문의 자동 분류 및 알림 시스템
1. 반복 업무 정의
고객 문의가 접수될 때마다 담당자가 문의 내용을 확인하고 문의 유형을 직접 분류한 후 Google Sheets에 기록하고 담당자에게 메신저로 전달하는 반복 업무를 자동화하였다.

기존에는 문의 내용을 사람이 직접 확인해야 했지만, 자동화 이후에는 Webhook으로 문의 데이터를 전달하면 시스템이 문의 유형을 판단하고 기록 및 알림을 자동으로 수행하도록 구성하였다.

2. 선정 도구
Make
Make를 자동화 도구로 선정하였다.

선정 이유는 다음과 같다.

Webhook을 Trigger로 사용할 수 있다.
Google Sheets와 Discord를 연결할 수 있다.
Router와 Filter를 이용해 문의 유형별 조건 분기를 구성할 수 있다.
전체 워크플로우를 시각적으로 확인하기 쉽다.
향후 생성형 AI를 추가하여 문의 내용을 자동 요약하거나 분류하는 방식으로 확장할 수 있다.
3. 워크플로우 설계
[Webhook]
     ↓
[문의 데이터 수신]
     ↓
   [Router]
  ↙    ↓    ↘
결제   배송   기타
 ↓      ↓      ↓
Sheets Sheets Sheets
 ↓      ↓      ↓
Discord Discord Discord

4. Trigger
Webhook을 Trigger로 설정하였다.

고객 이름, 이메일, 문의 내용을 Webhook 데이터로 전달하면 자동화가 실행된다.

예시 데이터:

{
  "name": "김민수",
  "email": "minsu@example.com",
  "message": "결제한 상품을 환불하고 싶습니다."
}

5. 조건 분기
Router를 이용하여 문의 유형을 세 가지로 분류하였다.

결제
문의 내용에 결제 또는 환불과 관련된 단어가 포함되면 결제 경로로 전달한다.

배송
문의 내용에 배송 또는 택배와 관련된 단어가 포함되면 배송 경로로 전달한다.

기타
앞의 조건에 해당하지 않는 문의는 기타 경로로 전달한다.

6. Action
각 분기에서는 두 개의 Action을 수행한다.

첫 번째 Action은 Google Sheets에 문의 정보를 기록하는 것이다.

두 번째 Action은 Discord에 담당자에게 전달할 알림을 보내는 것이다.

따라서 각 분기에는 최소 2개의 Action이 포함된다.

7. 테스트 결과
테스트 1
문의 내용:

결제한 상품을 환불하고 싶습니다.

결과:

Webhook → 결제 Route → Google Sheets → Discord

결제 경로가 정상적으로 실행되었다.

테스트 2
문의 내용:

주문한 상품의 배송이 언제 도착하나요?

결과:

Webhook → 배송 Route → Google Sheets → Discord

배송 경로가 정상적으로 실행되었다.

테스트 3
문의 내용:

회원정보를 변경하고 싶습니다.

결과:

Webhook → 기타 Route → Google Sheets → Discord

기타 경로가 정상적으로 실행되었다.

8. 기대 효과
자동화를 통해 반복적으로 수행하던 문의 분류와 기록 작업을 줄일 수 있다.

또한 문의가 접수되는 즉시 담당자에게 알림을 전달할 수 있으므로 담당자가 새로운 문의를 확인하는 데 필요한 시간을 줄일 수 있다.

향후 생성형 AI를 추가하면 문의 내용을 자동으로 요약하거나 더 다양한 유형으로 분류하는 기능까지 확장할 수 있다.

9. 결론
이번 프로젝트를 통해 Webhook이라는 Trigger를 이용해 외부에서 데이터를 전달받고, Router를 이용해 조건별로 데이터를 분기한 뒤 Google Sheets와 Discord를 Action으로 연결하는 자동화 과정을 구현하였다.

이를 통해 Trigger는 자동화를 시작하는 이벤트이고, Action은 실제 작업을 수행하는 단계이며, Router와 Filter는 조건에 따라 실행 경로를 결정하는 기능이라는 것을 실제 구현을 통해 확인하였다.

9. 프로젝트 2 캡처 목록
제출할 때는 다음 화면을 준비하면 됩니다.

Make 전체 워크플로우 화면

Webhook
Router
결제/배송/기타 3개 분기
Google Sheets
Discord
결제 조건 설정 화면

배송 조건 설정 화면

기타 경로 설정 화면

결제 테스트 실행 기록

배송 테스트 실행 기록

기타 테스트 실행 기록

Google Sheets 결과 화면

Discord 알림 결과 화면

10. 최종 제출물 체크리스트
프로젝트 1
 Make로 워크플로우 구현
 Zapier로 동일한 워크플로우 구현
 Trigger 1개 이상
 Action 2개 이상
 조건 분기 포함
 합격 경로 실제 실행
 불합격 경로 실제 실행
 Make 구성 화면 캡처
 Zapier 구성 화면 캡처
 실행 결과 캡처
 비교 분석 보고서 작성
 비교 항목 5개 이상
 각 도구 장단점 작성
 상황별 적합성 작성
프로젝트 2
 반복 업무 정의
 Make 선정 이유 작성
 Webhook Trigger 구현
 Router 조건 분기 구현
 결제 경로 실행
 배송 경로 실행
 기타 경로 실행
 Google Sheets 기록
 Discord 알림
 워크플로우 설명 작성
 구현 화면 캡처
 실행 결과 캡처
