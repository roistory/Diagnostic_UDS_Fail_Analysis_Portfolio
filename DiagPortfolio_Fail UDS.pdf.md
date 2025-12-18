
# Vehicle Diagnostic Communication – UDS Failure Analysis

**UDS (Extended CAN) Communication Failure Analysis**  
Log-based Reverse Engineering Portfolio

- **Role:** Vehicle Diagnostic Communication Reverse Engineer
    
- **Focus Areas:** UDS / Extended CAN / Communication Failure Analysis
    
- **Analysis Data:** Vehicle ↔ Diagnostic Tool Communication Logs (Single Diagnostic Session)
    
- **Security-sensitive information removed / Security logic abstracted**
    

---

## 1. Introduction

### 1.1 Project Background

During real vehicle operation, **intermittent UDS communication failures** were observed.  
Under the same vehicle conditions, the OEM diagnostic tool completed the diagnostic session normally,  
while a third-party diagnostic tool experienced communication failure.

The only available analysis data was **communication logs between the diagnostic tool and the vehicle**.

---

### 1.2 Project Scope

- Target protocol: UDS over Extended CAN
    
- Data limitation: Single diagnostic session log
    
- Excluded from analysis:
    
    - OEM brand names
        
    - Actual security algorithms
        
    - ECU internal source code
        

---

## 2. Problem Definition

### 2.1 Observed Symptoms

- Intermittent failure during diagnostic session entry
    
- ECU enters a no-response state after specific requests
    
- No issues observed when using the OEM diagnostic tool under the same conditions
    

### 2.2 Analysis Constraints

- No access to ECU firmware
    
- No OEM proprietary documentation
    
- Analysis limited to CAN communication logs
    

---

## 3. Analysis Environment

### 3.1 Tools Used

- CAN communication log capture tools
    
- Log filtering and manual decoding
    
- ISO 14229 (UDS) standard documentation
    
- Internal diagnostic simulator
    

### 3.2 Analysis Data Description

- Single vehicle diagnostic session
    
- Request / Response-based CAN frames
    
- Extended CAN ID format (18DAxxxx)
    

---

## 4. Diagnostic Communication Flow Reconstruction

### 4.1 OEM Diagnostic Tool Sequence

`Pre-condition Check → Diagnostic Session Control → Security Access → ECU Identification → Tester Present (Session Keep-Alive) → Diagnostic Service Execution`

### 4.2 Request / Response Mapping

|Step|Service|SID|Description|
|---|---|---|---|
|1|ReadDataByIdentifier|0x22|Pre-condition check|
|2|DiagnosticSessionControl|0x10|Extended session entry|
|3|SecurityAccess|0x27|High-level security access|
|4|ECU ID Read|0x22|ECU identification|
|5|Tester Present|0x3E|Session keep-alive|
|6|DTC Read|0x19|Diagnostic Trouble Code read|

---

## 5. Failure Pattern Analysis

### 5.1 Main Failure Types

- ECU no response after session request
    
- Timeout during security access
    
- Negative Response caused by unsupported sub-function
    
- Silent failure without NRC
    

### 5.2 Key Observations

The communication failures were **not random**.  
They occurred repeatedly under **specific conditions**, which indicates the existence of  
**condition-based logic inside the ECU**.

---

## 6. Key Finding #1 – Pre-condition Logic

### 6.1 Observation

All successful diagnostic sessions executed a specific  
`ReadDataByIdentifier` request **before** entering the diagnostic session.

Sessions that skipped this step tended to fail immediately.

### 6.2 Comparison with ISO 14229

- ISO 14229:
    
    - No mandatory voltage check defined before session entry
        
- Actual behavior:
    
    - ECU responds to session requests **only when specific pre-conditions are met**
        

### 6.3 Interpretation

This behavior is considered an **OEM-specific safety pre-condition logic**  
implemented at the ECU level.

### 6.4 Additional Insight

Many OEM diagnostic systems appear to implement a Battery Voltage Check  
before starting diagnostic communication.

This is likely a preventive measure to avoid ECU damage caused by  
power loss during diagnostic operations such as actuator control or special functions.

---

## 7. Key Finding #2 – Session Timing Sensitivity

### 7.1 Observation

- ECU response delay is not constant
    
- Fixed timeout values cause false communication failures
    

### 7.2 Analysis Result

|Configuration|Result|
|---|---|
|Fixed 500 ms timeout|False failure detected|
|Variable timeout (500–5000 ms)|Stable communication|

### 7.3 Conclusion

A **variable timing strategy** is required for reliable diagnostic communication.

---

## 8. Key Finding #3 – Security Access Dependency

### 8.1 Observation

- High-level security access is required before executing high-risk diagnostic services
    
- The overall flow follows the UDS security access procedure
    

### 8.2 Security Handling Policy

- Actual encryption algorithms are excluded from analysis
    
- Only the access flow and dependency are analyzed
    

→ Structural understanding is demonstrated without compromising security.

---

## 9. Fail-Safe Design

### 9.1 Recommended Diagnostic Strategy

1. Always validate pre-conditions before starting diagnostics
    
2. Apply variable timeout handling based on ECU response behavior
    
3. Verify session state before executing diagnostic services
    
4. Retry operations only after re-entering the diagnostic session
    
5. Perform safe termination when ECU enters a no-response state
    

### 9.2 Expected Benefits

- Reduced communication failure rate in real vehicle environments
    
- Improved diagnostic stability
    
- Minimized risk during safety-critical diagnostic operations
    

---

## 10. Conclusion

This project demonstrates the following capabilities, even under limited information conditions:

- OEM-specific implementations can be derived using log analysis alone
    
- International standards can be interpreted and applied in real-world environments
    
- Reverse engineering results can be translated into safety-focused system design
    

---

## 11. Appendix

### A. Log Example (Sensitive Data Removed)

`REQ: 18DA00FA 03 22 XX XX`  
`RES: 18DAFA00 05 62 XX XX YY`

### B. Terminology

- **UDS:** Unified Diagnostic Services
    
- **NRC:** Negative Response Code
    
- **Extended CAN:** 29-bit CAN Identifier


# 자동차 진단 통신 – UDS 실패 분석

**UDS (Extended CAN) 통신 실패 분석**  
로그 기반 리버스 엔지니어링 포트폴리오

- 역할: 자동차 진단 통신 리버스 엔지니어
    
- 중점 분야: UDS / Extended CAN / 통신 실패 분석
    
- 분석 데이터: 차량 ↔ 진단기 통신 로그 (단일 진단 세션)
    
- 보안 민감 정보 제거 / 시큐리티 로직 추상화
    

---

## 1. 서론 (Introduction)

### 1.1 프로젝트 배경

실차 환경에서 자동차 진단 시스템을 운용하던 중, **간헐적인 UDS 통신 실패**가 발생
동일한 차량 조건에서 OEM 진단기는 정상적으로 진단 세션을 완료했으나,  
서드파티 진단기는 통신 실패가 발생

분석 가능한 자료는 **진단기와 차량 간의 통신 로그**

---

### 1.2 프로젝트 범위

- 대상 프로토콜: UDS over Extended CAN
    
- 데이터 제약: 단일 진단 세션 로그
    
- 분석 제외 항목:
    
    - OEM 브랜드명
        
    - 실제 보안 알고리즘
        
    - ECU 내부 소스 코드

---

## 2. 문제 정의 (Problem Definition)

### 2.1 관찰된 증상

- 진단 세션 진입 단계에서 간헐적 실패 발생
    
- 특정 요청 이후 ECU 무응답 상태 진입
    
- OEM 진단기에서는 동일 조건에서 문제 미발생

### 2.2 분석 제약 조건

- ECU 펌웨어 접근 불가
    
- OEM 전용 문서 부재
    
- CAN 로그 기반 분석만 가능

---

## 3. 분석 환경 (Analysis Environment)

### 3.1 사용 도구

- CAN 통신 로그 캡처 도구
    
- 로그 필터링 및 수동 디코딩
    
- ISO 14229 (UDS) 표준 문서
    
- 내부 진단 시뮬레이터

### 3.2 분석 데이터 설명

- 단일 차량 진단 세션
    
- Request / Response 기반 CAN 프레임
    
- Extended CAN ID (18DAxxxx) 사용
---

## 4. 진단 통신 흐름 재구성

### 4.1 OEM 진단기 시퀀스

`사전 조건 검증  → 진단 세션 제어  → 보안 접근  → ECU 식별  → Tester Present (세션 유지)  → 진단 서비스 수행`

### 4.2 요청/응답 매핑 결과

| 단계  | 서비스                      | SID  | 설명       |
| --- | ------------------------ | ---- | -------- |
| 1   | ReadDataByIdentifier     | 0x22 | 사전 조건 확인 |
| 2   | DiagnosticSessionControl | 0x10 | 확장 세션 진입 |
| 3   | SecurityAccess           | 0x27 | 상위 보안 레벨 |
| 4   | ECU ID 조회                | 0x22 | ECU 식별   |
| 5   | Tester Present           | 0x3E | 세션 유지    |
| 6   | DTC Read                 | 0x19 | 고장 코드 조회 |

---

## 5. 실패 패턴 분석 (Failure Pattern Analysis)

### 5.1 주요 실패 유형

- 세션 요청 이후 ECU 무응답
    
- 보안 접근 단계에서 타임아웃
    
- 지원되지 않는 Sub-function 요청에 따른 Negative Response
    
- NRC 없이 Silent Fail 발생


### 5.2 핵심 관찰 결과

통신 실패는 **무작위가 아니라 특정 조건에서 반복적으로 발생**하였다.  
이는 ECU 내부에 **조건 기반 로직**이 존재함을 시사한다.

---

## 6. 주요 발견 #1 – 사전 조건 로직

### 6.1 관찰 내용

모든 성공한 진단 세션은  
진단 세션 진입 이전에 특정 `ReadDataByIdentifier` 요청을 수행하였다.

이 단계를 생략한 세션은 즉시 실패하는 경향을 보였다.

### 6.2 ISO 14229 표준 비교

- ISO 14229:
    
    - 세션 진입 전 필수 전압 체크 명시 없음
        
- 실제 동작:
    
    - ECU가 특정 사전 조건 만족 시에만 세션 요청에 응답
    - 
### 6.3 해석

이는 ECU 레벨에서 구현된 **OEM 고유의 안전성 사전 조건 로직**으로 판단된다.

### 6.4 비교

다수의 OEM 진단 통신 시작 전 Battery Voltage Check 기능이 구현된 것으로 보인다.

진단 기능 실행 중(강제 구동, 부가 기능 등) 진단기 전원이 꺼져 ECU가 고장나지 않게 하기 위한 사전 조치로 보임임

---

## 7. 주요 발견 #2 – 세션 타이밍 민감성

### 7.1 관찰 내용

- ECU 응답 지연 시간이 일정하지 않음
    
- 고정 타임아웃 사용 시 정상 세션도 실패 처리됨

### 7.2 분석 결과

| 설정                   | 결과        |
| -------------------- | --------- |
| 고정 500ms 타임아웃        | 오탐 실패 발생  |
| 가변 타임아웃 (500~5000ms) | 안정적 통신 유지 |

### 7.3 결론

신뢰성 있는 진단 통신을 위해서는 **가변 타이밍 로직**이 필수적이다.

---

## 8. 주요 발견 #3 – 보안 접근 의존성

### 8.1 관찰 내용

- 고위험 진단 기능 수행 전 상위 보안 레벨 요구
    
- UDS 표준의 보안 접근 흐름을 따름

### 8.2 보안 처리 원칙

- 실제 암호화 알고리즘은 분석 대상에서 제외
    
- 접근 흐름과 의존성만 분석

→ 보안을 침해하지 않으면서 구조적 이해를 입증

---

## 9. Fail-Safe 설계

### 9.1 권장 진단 전략

1. 진단 시작 전 사전 조건 검증 필수
    
2. ECU 응답 시간에 따른 가변 타임아웃 적용
    
3. 진단 서비스 수행 전 세션 상태 검증
    
4. 실패 시 재시도는 세션 재진입 후 수행
    
5. ECU 무응답 시 안전 종료 처리
    

### 9.2 기대 효과

- 현장 통신 실패율 감소
    
- 진단 안정성 향상
    
- 안전 관련 기능 수행 시 리스크 최소화

---

## 10. 결론 (Conclusion)

본 프로젝트는 제한된 정보 환경에서도 다음을 입증한다.

- 로그 분석만으로 OEM 고유 구현 방식 도출 가능
    
- 국제 표준을 실무 관점에서 해석·적용 가능
    
- 리버스 엔지니어링 결과를 안정성 중심 설계로 전환 가능

---

## 11. 부록 (Appendix)

### A. 로그 예시 (민감 정보 제거)

`REQ: 18DA00FA 03 22 XX XX RES: 18DAFA00 05 62 XX XX YY`

### B. 용어 정리

- UDS: Unified Diagnostic Services
    
- NRC: Negative Response Code
    
- Extended CAN: 29비트 CAN Identifier

---


https://github.com/roistory/Diagnostic_UDS_Fail_Analysis_Portfolio
