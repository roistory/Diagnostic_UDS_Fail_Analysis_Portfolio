### Automotive Diagnostic Communication

### UDS (Extended CAN) Failure Analysis Portfolio

**Role:** Diagnostic Communication Reverse Engineer  
**Focus Areas:** UDS / Extended CAN / Communication Failure Analysis / OEM Exception Logic  
**Data Source:** Vehicle ↔ Diagnostic Tool Communication Logs (single diagnostic session)

---

### 1. Problem Statement

Intermittent **UDS communication failures** occurred during field operation of an automotive diagnostic system.  
OEM diagnostic tools completed sessions successfully, but the third-party diagnostic tool failed under the same vehicle conditions.

Available evidence: diagnostic tool → vehicle communication logs

---

### 2. Objective

This project uses log-based reverse engineering with these goals:

- Identify the root causes of diagnostic communication failures
    
- Reveal OEM-specific logic not in standards
    
- Produce fail-safe diagnostic communication guidelines suitable for real vehicles
    

---

### 3. Methodology

- Collect UDS (Extended CAN) diagnostic session logs
    
- Reconstruct the diagnostic flow:
    
    - Pre-condition checks
        
    - Diagnostic session control
        
    - Security access procedures
        
    - ECU identification
        
    - Diagnostic service execution
        
- Compare successful vs failed logs and make inferences
    
- Verify findings against ISO 14229 (UDS) requirements
    

---

### 4. Key Findings

**① Discovery of a pre-condition logic**

- A Battery Voltage check is required before session entry
    
- This check is not in ISO 14229 but is implemented on many OEM tools
    
- When the pre-condition is not met, the ECU goes into a no-response state
    

**② Variable session timing requirements**

- ECU response timeouts varied between about 500 ms and 5000 ms
    
- Using a fixed timeout caused false failures when the ECU responded more slowly
    

**③ Security access dependency**

- High-risk diagnostic functions require a higher security level before they run
    
- The analysis focuses on the access flow; cryptographic details are excluded
    

---

### 5. Outcome

- Root causes of communication failures were clarified
    
- OEM-specific exception handling logic was identified
    
- Diagnostic stability was improved by:
    
    - Enforcing required pre-condition checks
        
    - Supporting adaptive timeout handling
        
- Findings were documented into an internal diagnostic guideline
    

---

### 6. Significance

This project demonstrates these capabilities:

- Deriving OEM-specific behavior from limited log data
    
- Interpreting international standards from a practical perspective
    
- Turning reverse-engineering results into safety-aware system design



### 자동차 진단 통신

### UDS (Extended CAN) 실패 분석 포트폴리오

**역할:** 자동차 진단 통신 리버스 엔지니어링  
**중점 분야:** UDS / Extended CAN / 통신 실패 분석 / OEM 예외 로직  
**분석 데이터:** 차량 ↔ 진단기 통신 로그 (단일 진단 세션)

---

### 1. 문제 정의

자동차 진단 시스템 현장 운용 중 **간헐적인 UDS 통신 실패** 발생
OEM 진단기는 정상적으로 진단을 완료하였으나, 동일 차량 조건에서  
진단기는 통신 실패가 발생하는 현상이 확인.

분석 가능 자료 : 진단기 > 자동차 통신 로그

---

### 2. 목표

본 프로젝트는 통신 로그 기반 리버스 엔지니어링을 통해 다음을 목표로 설정

- 진단 통신 실패의 근본 원인 규명
    
- OEM 고유 로직 도출
    
- 실차 환경에 적용 가능한 Fail-Safe 진단 통신 가이드라인 수립

---

### 3. 분석 방법

- UDS 기반 Extended CAN 진단 세션 로그 수집
    
- 진단 통신 흐름 재구성:
    
    - 사전 조건 검증
        
    - 진단 세션 제어
        
    - 보안 접근 절차
        
    - ECU 식별
        
    - 진단 서비스 수행
        
- 정상 로그와 실패 로그 비교 분석 및 추정
    
- ISO 14229(UDS) 표준과의 요구 사항 비교 검증


---

### 4. 주요 분석 결과

**① 사전 조건 로직 발견**

- 진단 세션 진입 전 Battery Voltage 체크가 필수적으로 요구됨
    
- 해당 로직은 ISO 14229 표준에 명시되지 않지만 다수의 OEM 진단기에 기능 첨부
    
- 사전 조건 미충족 시 ECU가 무응답 상태로 진입


**② 가변적인 세션 타이밍 요구사항**

- ECU 응답 타임아웃이 500ms ~ 5000ms 범위로 변동
    
- 고정 타임아웃 로직 사용 시 무응답 실패 발생

**③ 보안 접근 의존성 확인**

- 고위험 진단 기능 수행 전 상위 보안 레벨 요구
    
- 실제 보안 알고리즘은 배제하고 접근 흐름만 분석
---

### 5. 결과

- 통신 실패 원인 명확화
    
- OEM 고유 예외 처리 로직 도출
    
- 다음 요소를 통해 진단 안정성 향상:
    
    - 필수 사전 조건 검증
        
    - 가변 타임아웃 처리
    
- 분석 결과를 내부 진단 가이드라인으로 문서화

---

### 6. 의의

본 프로젝트는 다음 역량을 입증합니다.

- 제한된 로그 정보만으로 OEM 고유 구현 방식 도출
    
- 국제 표준을 실무 관점에서 해석하고 적용
    
- 리버스 엔지니어링 결과를 안전 중심 설계로 전환하는 능력