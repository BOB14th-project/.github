# 🛡️ CryptoScanner (BOB 14th)
양자내성(PQC) 전환을 위해 레거시 암호(RSA/ECC, SHA-1 등)가 쓰이는 위치를 자동으로 찾고 분석하는 멀티플랫폼 스캐너입니다. 실행파일·소스코드·키/인증서를 한 번에 훑어 PQC 전환 우선순위를 잡을 수 있게 도와줍니다.

## 프로젝트 필요성
- 양자 환경 대비를 위해 보유 자산의 구형 암호 사용처를 빠르게 인벤토리화해야 함.
- 코드·바이너리·키가 여러 플랫폼에 흩어져 있어 수동 점검 시 누락/지연 위험이 큼.
- 개발·운영 파이프라인(SBOM/공급망)에서 암호 의존성을 조기에 식별해 전환 리스크를 줄여야 함.
- 참고
    - NIST Post-Quantum Cryptography Project(https://csrc.nist.gov/projects/post-quantum-cryptography)
    - NIST “Migration to Post-Quantum Cryptography”(2021)
    - NSA CNSA 2.0 전환 가이드(2022)

## 탐지 흐름
- 입력: 파일·폴더 단위 선택 또는 전체 시스템 스캔.
- 정적 분석: tree-sitter로 소스 AST 파싱, LLVM/objdump 기반 바이너리·라이브러리 시그니처 매칭.
- 동적 분석: 실행 중 TLS/암호 API 호출 후킹·로그 수집으로 키/인증서 사용 패턴 확인.
- 결과 통합: 알고리즘/키 길이/라이브러리별 통계, 파일별 상세 내역 집계.
- LLM 분석/요약: AI 서버가 스캔 결과를 요약·우선순위화하고 전환 권고 초안을 생성(AI 벤치마크 결과 기반 모델 선택).
- 리포트/저장: 요약·통계·상세를 리포트/CSV로 제공하고 DB에 저장.

```mermaid
flowchart TD
    S[스캔 시작] --> T{스코프 선택<br/>파일/폴더 vs 전체 시스템}
    T --> SA[정적 분석<br/>AST 파싱·시그니처 매칭]
    T --> DA[동적 분석(선택)<br/>TLS/암호 API 후킹·로그]
    SA --> R[결과 통합]
    DA --> R
    R --> L[LLM 분석·요약<br/>권고 생성]
    L --> V1[통계/차트]
    L --> V2[파일별 상세]
    V1 --> X[리포트·CSV]
    V2 --> X
    X --> D[DB 저장]
```
차트 수정 필요


## 주요 기능/특징
- 정적(C++) + 동적 분석 백엔드 통합, tree-sitter/LLVM/Qt 기반
- Electron + React GUI로 Quick Scan, 시스템 전체 Full Scan 제공
- 스캔 이력/CSV 다운로드, 알고리즘·타입 통계 차트, 파일별 상세 결과 확인
- macOS(ARM), Linux(AMD/ARM), Windows(AMD) 빌드/배포 지원
- LLM 기반 리포트 생성·요약을 위한 AI 서버 연동 준비

## 레포 구조
- `CryptoScanner/` 정적 분석 백엔드
- `DynamicAnalysis/` 동적 분석 백엔드 (플랫폼별 소스 & 스크립트)
- `crypto-scanner-gui/` Electron + React UI, 빌드 시 백엔드 복사 스크립트 포함
- `LLM-Report/` LLM Orchestration 리포트 템플릿
- `mac-arm64 | linux-amd | linux-arm | win-amd/` 빌드 산출물

## 연계 컴포넌트
- AI 서버: https://github.com/BOB14th-project/AI-Server — LLM 호출과 리포트/요약 오케스트레이션
- DB: https://github.com/BOB14th-project/db — 스캔 결과·메타데이터 저장
- AI 벤치마킹: https://github.com/BOB14th-project/AI--Benchmark — 모델 품질·성능 비교, 운영 모델 선택 근거

## 팀
- 개발 기간: 2025.09 ~ 2025.12

## 링크
- 메인 저장소: https://github.com/BOB14th-project/CryptoScanner_GUI
