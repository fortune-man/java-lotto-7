# java-lotto-precourse
## 이번 미션 소묙표
[] 오버 엔지니어링만 하지 말자
- 아 벌써 예외 처리에서 오바했다
- kiss! keep it small and simple 해보자


### knwon issue

- 우선순위 정렬 필요
- 임팩트 순서로
  1. 미션 통과 -> 고객 요구에 근거한 최소한의 소목표 달성
     1.1 고객과의 신뢰 잃지 않는 것을 최소한으로
  2. 이번주 목표 달성 -> 오버 엔지니어링 방지 -> 실패하는 테스트 10개, 통과 6개 (달성률 약 37%)
  3. 해야될게 많을수록 하나씩
     1.1 오전 코딩으로 다행히 조금씩 진척 만들어놓음
     1.2 하나씩 하나씩 해나가며 끝까지를 목표로
---

### 기능 목록 프레임워크

1. 문제를 명확히 이해했는지 확인 (나만의 언어로 문제를 재설명)
   1. given : 1~45 범위의 정수 로또 번호
   2. when : 1개의 로또 발행할 때 중복되지 않는 6개의 숫자 조회
   3. 당첨 번호 추첨
      1. 중복되지 않는 숫자 6개, 보너스 번호 1개 총 7개 조회
   4. 로또 구입 금액을 입력하면 구입 금액에 해당하는 만큼 로또를 발행
      1. 당첨은 1등부터 5등까지
      2. 당첨기준과 금액은 3과 같음
      3. 1등: 6개 번호 일치 / 2,000,000,000원
         2등: 5개 번호 + 보너스 번호 일치 / 30,000,000원
         3등: 5개 번호 일치 / 1,500,000원
         4등: 4개 번호 일치 / 50,000원
         5등: 3개 번호 일치 / 5,000원
      4. 로또 1장의 가격은 1,000원
   5. then: 당첨 번호와 보너스 번호까지 입력하면 사용자가 구매한 로또 번호와 당첨 번호를 비굫여 당첨 내역 및 수익률을 출력하고 로또 게임을 종료
      1. 사용자가 잘못된 값을 입력할 경우 IllegalArgumentException
      2. 에러 미시지 출력 후 그 부분 입력을 다시 요청
      3. 이 때 그냥 Exception 클래스가 아닌 IllegalArgumentException, IllegalStateException 등과 같은 명확한 유형을 처리
2. 가정할 수 있는 것들을 확인
   1. 입력 가능 범위는 정해져있는가?
   2. 실제 로또 최대 구매비용은 10만원인데 동일한 상황으로 고려해야 되는가? (사용자가 구매할 수 있는 로또는 최대 100장..?)
   3. 만약 엣지 케이스가 있다면 null이라고 해야하는가?
      4. 쉼표를 포함하여 구입 금액을 입력하는 경우 (예: 1,400)
      5. 문자열을 포함하여 입력하는 경우 (예: 만원, 1만원, 10,000원)
   4. 아니라고 가정할 수 있는건가?
      1. 구입 금액 입력은 정수 입력만 허용하도록 강제해도 되는건가?
      2. 그게 사용성을 해쳐서 고객 이탈을 유도하진 않는가?
      3. 개발 경험과 고객 경험을 저울질 해도 되는가?
      4. 이것이 유효한 고민인가 또는 다른 것에 신경써야 하는가?
         3. 예시를 들어가면서 확인
            1. 애플리케이션을 실행하면 사용자에게 구입금액 입력 문구를 화면에 제공. 예시는
               구입금액을 입력해 주세요.
               8000
            2. 구매 결과를 내부 처리해서 결과를 제공. 예를 들어 8000원을 입력해서 8개를 구매했다면 예상되는 결과는
               8개를 구매했습니다.
               [8, 21, 23, 41, 42, 43]
               [3, 5, 11, 16, 32, 38]
               [7, 11, 16, 35, 36, 44]
               [1, 8, 11, 31, 41, 42]
               [13, 14, 16, 38, 42, 45]
               [7, 11, 30, 40, 42, 43]
               [2, 13, 22, 32, 38, 45]
               [1, 3, 5, 14, 22, 45]
            3. 그 다음 당첨 번호와 보너스 번호를 입력하면 당첨 통계와 총 수익률을 각각 카운팅 / 계산해서 사용자에게 출력함으로써 제공하게 되고 그 예시는
               당첨 번호를 입력해 주세요.
               1,2,3,4,5,6

               보너스 번호를 입력해 주세요.
               7
   
            당첨 통계
            ---
            3개 일치 (5,000원) - 1개
            4개 일치 (50,000원) - 0개
            5개 일치 (1,500,000원) - 0개
            5개 일치, 보너스 볼 일치 (30,000,000원) - 0개
            6개 일치 (2,000,000,000원) - 0개
            총 수익률은 62.5%입니다.
   
4. 문제 해결책(기능 목록)을 고민
   1. 입력
      1. 구매 금액 입력
      2. 당첨 번호 입력
      3. 보너스 번호 입력
   2. 처리
      1. 구매 금액에 근거한 로또 발행
      2. 당첨 번호와 보너스 번호 조회
      3. 로또 수 (구매 금액 / 로또 금액) 만큼 발행된 로또와 당첨 번호 보너스 번호를 적절히 비교
      4. 비교한 결과를 기반으로 총 수익률 계산
   3. 출력
      1. 발행된 로또를 조회하여 출력
      2. 2.3 을 조회하여 출력
      3. 2.4 를 조회하여 출력
   4. 예외 처리
      1. 사용자가 잘못된 값 입력할 경우 예외 처리
      2. 입력 가능 범위 초과 : IllegalArgumentException
      3. 문자열 포함 입력 : NumberFormatException
5. 4에 대한 설명
   1. 애플리케이션 실행시 사용자가 구매 금액을 입력하면 
   2. 구매 결과를 기반으로 로또 발행 기능을 실행하고 발행한 로또를 조회하여 출력
   3. 그 다음 사용자가 당첨 번호를 입력하고 
   4. 보너스 번호까지 입력하면 
   5. 내부적으로 구매 금액만큼의 로또를 발행하여 당첨 번호와 보너스 번호를 적절히 비교 하고
   6. 비교한 결과를 토대로 총 수익률 계산 
   7. 당첨 번호 / 보너스 번호 비교 결과를 조회하여 각각 당첨 통계와 수익률을 출력하고 애플리케이션 종료
   8. 사용자가 잘못된 값을 입력할 경우 예외 처리
      1. 예외 발생 원인을 명확히 전달하는 유형 사용
      2. 예1) 입력 가능 범위를 초과한 경우 IllegalArgumentException 예외 처리하며 로또 구매 가능 금액 문구를 출력하고 정상 문구 재입력을 요청
      3. 예2) 문자열이 포함된 값을 입력한 경우 NumberFormatException 예외 처리로 에러 메시지를 출력하고 정상 값 재입력을 요청
6. 5 결과 진단 후 진단 결과가 최선인지 재확인
   1. 예외 처리 과정이 정상적인지 재검수 필요할 수 있음
   2. 처리 객체에서 할당된 책임이 너무 많아보여 분리가 필요해보임. 분리 후 처리 객체의 내부 책임들을 다른 곳으로 조금씩 이동시키는 연습을 제안해봄
   3. 프로그래밍 요구사항 1, 2, 3 적용이 필요해보임
      1. [] indent depth는 2까지
      2. [] 3항 연산자 x, 
      3. [] 함수가 한 가지 일만 하도록 최대한 작게 만들기 위해 15라인을 넘기지 않는다
      4. [] else, switch / case 사용 x. (if 조건문에서 값을 return하는 방식으로 바꾸면 else 사용 필요 없어짐)
      5. [] Java Enum 적용
      6. [] 구현 기능에 대한 단위 테스트 작성. (UI 로직은 제외, System.out, System.in, Scanner..)
      7. [] Lotto 클래스 사용, numbers외 필드 추가 x, numbers 필드 접근 제어자 변경 x, 패키지 변경은 ok
7. 실패하는 테스트 코드부터 작성

진행률 6 / 7

---
#### 액션 아이템
0 / 12
1. 화요일 1800 부터 실패하는 테스트 코드 하나씩 작성
2. 일요일 1500까지  통과하는 테스트 코드로 하나씩 개선
3. 월요일 2359까지 하나씩 리팩토링
   1. [] 프로그래밍 요구사항 1, 2
   2. [] java style guide
   3. [] angularjs git commit message convention
   4. [] junit5
   5. [] 2주차 공통 피드백 하나씩 적용
   6. [] 1주차 공통 피드백 하나씩 적용
4. 학습 내용 하나씩 정리
   1. [] oracle java
   2. [] junit5
   3. [] java style guide
   4. [] angularjs git commit message convention