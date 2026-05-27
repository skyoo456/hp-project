# HomePlate

HomePlate는 야구 종합 티켓팅 서비스를 주제로 진행한 팀 프로젝트입니다.  
본 저장소는 기업 제출용 포트폴리오 정리본으로, 메인 티켓팅 서비스 프론트엔드, EKS 기반 Observability 구성, 생성형 AI 기반 AIOps 운영 보조 시스템을 함께 포함합니다.

## 프로젝트 개요

HomePlate는 사용자가 야구 경기 정보를 확인하고, 좌석을 선택해 티켓을 예매하는 흐름을 구현한 웹 서비스입니다.

본 프로젝트는 단순한 서비스 기능 구현에 그치지 않고, 실제 운영 상황에서 서비스 상태를 관측하고 문제 원인을 빠르게 파악할 수 있도록 모니터링 환경과 AIOps 구조를 함께 설계했습니다.

특히 본인은 프론트엔드 개발과 함께, 운영자가 메트릭·로그·트레이스·알림 정보를 웹 브라우저에서 확인하고 생성형 AI 요약을 통해 운영 이슈를 빠르게 파악할 수 있는 구조를 담당했습니다.

## 본인 담당 역할

- 메인 티켓팅 서비스 프론트엔드 개발
- 사용자 관점 이벤트 및 에러 수집을 위한 RUM 구조 설계
- AWS EKS 기반 Observability 구성 정리
- Prometheus, Loki, Tempo, Grafana 기반 모니터링 구조 설계
- Grafana Dashboard 및 PrometheusRule 정리
- Alertmanager 알림 기반 AIOps Case 생성 흐름 설계
- Prometheus, Loki, Tempo 데이터를 수집하는 AIOps 백엔드 개발
- 수집된 운영 정보를 웹 브라우저에서 확인할 수 있는 AIOps 포털 구현
- Amazon Bedrock 기반 생성형 AI 요약 기능 연동

## 주요 기능

### 1. 메인 티켓팅 서비스 프론트엔드

- 경기 목록 및 경기 상세 화면
- 좌석 구역 및 좌석 선택 UI
- 예매 흐름 구현
- 티켓 확인 화면
- 사용자 인증 화면
- 관리자용 화면 일부 구현
- RUM 기반 사용자 이벤트 및 에러 수집 구조 구성

### 2. Observability 구성

- Prometheus 기반 메트릭 수집
- Loki 기반 로그 조회
- Tempo 기반 트레이스 조회
- Grafana 대시보드 구성
- Alertmanager 알림 구조 구성
- ServiceMonitor, PrometheusRule, Grafana Dashboard 관리
- Helm과 Kustomize를 활용한 EKS 운영 리소스 정리

### 3. AIOps 운영 보조 시스템

- Alertmanager Webhook 수신
- 알림 기반 Case 생성
- Case별 Prometheus, Loki, Tempo Snapshot 수집
- 생성형 AI 기반 요약 생성
- 운영자가 웹 브라우저에서 알림, 근거 데이터, AI 요약을 함께 확인할 수 있는 UI 제공
- 주간 리포트 기능 일부 구현

## 기술 스택

### Frontend

- Next.js
- React
- TypeScript
- Tailwind CSS
- Zustand
- TanStack Query
- Grafana Faro RUM

### AIOps Backend

- Python
- FastAPI
- PostgreSQL
- asyncpg
- boto3
- Amazon Bedrock
- Prometheus API
- Loki API
- Tempo API

### Observability / Infra

- AWS EKS
- Kubernetes
- Helm
- Kustomize
- Prometheus
- Loki
- Tempo
- Grafana
- Alertmanager
- Grafana Alloy
- YACE
- Istio VirtualService
- External Secrets

## 저장소 구조

```text
.
├─ homeplate_frontend/
│  └─ 메인 야구 티켓팅 서비스 프론트엔드
│
├─ homeplate_aiops/
│  ├─ ops-portal/
│  │  └─ AIOps 백엔드 API
│  │
│  └─ ops-site/
│     └─ AIOps 운영 포털 프론트엔드
│
└─ homeplate-obs-eks-portfolio-staging/
   └─ EKS 기반 Observability, Alerting, Dashboard, IaC 정리본
```

## 시스템 흐름

### 서비스 운영 관측 흐름

```text
User
→ HomePlate Frontend
→ Backend APIs
→ Metrics / Logs / Traces / RUM
→ Prometheus / Loki / Tempo
→ Grafana Dashboard
→ Alertmanager
```

### AIOps 흐름

```text
Alertmanager
→ AIOps Backend
→ Case 생성
→ Prometheus / Loki / Tempo 데이터 수집
→ 생성형 AI 요약
→ AIOps Web UI에서 확인
```

## 주요 설계 포인트

### Observability

운영 상황에서 문제를 빠르게 파악하기 위해 Metrics, Logs, Traces를 분리해 수집하고 Grafana에서 통합적으로 확인할 수 있도록 구성했습니다.

- Metrics: Prometheus
- Logs: Loki
- Traces: Tempo
- Dashboard: Grafana
- Alert: Alertmanager
- RUM: Grafana Faro

### AIOps

Alertmanager에서 발생한 알림을 단순 알림으로 끝내지 않고, 운영자가 확인해야 할 근거 데이터를 함께 수집하도록 구성했습니다.

AIOps 백엔드는 알림을 수신한 뒤 관련 메트릭, 로그, 트레이스 정보를 수집하고, 생성형 AI를 활용해 운영자가 이해하기 쉬운 형태의 요약 정보를 제공합니다.

이를 통해 운영자는 웹 브라우저에서 이슈별 상태, 근거 데이터, AI 요약을 함께 확인할 수 있습니다.

## 실행 방법

각 하위 프로젝트별 실행 방법은 하위 README를 참고합니다.

- `homeplate_frontend/README.md`
- `homeplate_aiops/ops-portal/README.md`
- `homeplate_aiops/ops-site/README.md`
- `homeplate-obs-eks-portfolio-staging/README.md`

## 보안 및 공개 범위

이 저장소는 포트폴리오 제출을 위해 정리한 공개용 저장소입니다.

- 실제 `.env`, `.env.local` 파일은 포함하지 않습니다.
- AWS Access Key, Slack Webhook, DB Password 등 실제 비밀값은 포함하지 않습니다.
- 운영 환경의 Secret은 External Secrets 및 AWS Secrets Manager 사용을 기준으로 설계했습니다.
- 일부 값은 예시 또는 placeholder 형태로만 포함했습니다.
- 팀 프로젝트 전체 코드 중 포트폴리오 제출에 필요한 범위를 중심으로 정리했습니다.

## 프로젝트 성과

- 야구 티켓팅 서비스의 사용자 흐름을 프론트엔드에서 구현했습니다.
- 서비스 운영 관점에서 Metrics, Logs, Traces, RUM을 활용한 관측 구조를 설계했습니다.
- Grafana Dashboard와 Alertmanager 알림 구조를 통해 서비스 상태를 계층적으로 확인할 수 있도록 구성했습니다.
- AIOps 포털을 통해 알림, 메트릭, 로그, 트레이스, AI 요약을 한 화면에서 확인하는 운영 보조 흐름을 구현했습니다.
- 사용자 관점의 RUM 데이터를 수집하여 프론트엔드 오류와 사용자 경험 저하를 추적할 수 있도록 설계했습니다.

## 참고

본 프로젝트는 팀 프로젝트이며, 본 저장소는 본인이 담당한 프론트엔드, Observability, AIOps 영역을 중심으로 정리한 포트폴리오용 저장소입니다.