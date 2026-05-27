# aws-eks

**HomePlate EKS** 환경에서 관측 가능성(Observability), 알림, AIOps 연동을 위한 Kubernetes·Helm·Kustomize 인프라 코드 저장소입니다.

## 역할 (담당 범위)

Observability / **EKS** / **Grafana** / **Loki** / **Tempo** / **Alerting**(Alertmanager, PrometheusRule) / **AIOps** 앱 배포 및 모니터링 연동을 다룹니다.

## Source of truth

실제 배포와 운영에 사용하는 기준 경로는 아래입니다.

| 경로             | 역할                                                                                |
| ---------------- | ----------------------------------------------------------------------------------- |
| **`helm/`**      | kube-prometheus-stack, Loki, Tempo, Alloy, YACE 등 서드파티 차트용 values           |
| **`kustomize/`** | 앱·ServiceMonitor·Exporter·Grafana 대시보드·PrometheusRule 등 운영 매니페스트       |
| **`infra/`**     | AlertmanagerConfig, ExternalSecret, IAM 정책·신뢰 정책 등 클러스터/계정 수준 리소스 |

참고·아카이브만 해당하는 경로:

- **`origins/`** — 과거 차트/참고용. 활성 배포 소스가 아님.
- **`aiops/`**, **`kps/`**, **`loki/`**, **`tempo/`** 등 저장소 루트의 동일 이름 디렉터리 — 레거시·미러; 활성 소스는 위 표를 따름.

## Secrets 정책

- **실제 비밀 값은 Git에 커밋하지 않습니다.** AWS Secrets Manager(또는 이에 상응하는 비밀 저장소)와 **ExternalSecret** / **SecretStore** 조합으로 클러스터에 주입하는 방향을 사용했습니다.
- 로컬 전용 파일(예: overlay의 `.env`)과 이름이 고정된 Secret YAML은 **`.gitignore`**로 제외합니다.
- 이 저장소에는 **`*.example.yaml`**, **`*.example.json`** 형태의 **스키마·플레이스홀더만** 포함합니다. 예:
  - `aiops/01-aiops-backend-secret.example.yaml`
  - `alertmanager-webhooks.example.json`
  - `kustomize/base/apps/aiops/04-aiops-frontend-auth.example.yaml` — `aiops-frontend-auth` Secret은 배포 시 생성하고, 매니페스트에는 Deployment/Service만 둡니다.

## 검증 방법

### Helm

```bash
helm lint <chart> -f <values-file>
helm template <release-name> <chart> -f <values-file> > /dev/null
```

### Kustomize

```bash
kubectl kustomize kustomize/base
kubectl kustomize kustomize/overlays/prod
kubectl apply --dry-run=server -k kustomize/overlays/prod
```

## 구조 (요약)

### helm/

서드파티 설치용 values만 둡니다.

- `helm/kps/` — kube-prometheus-stack
- `helm/alloy/` — Alloy 수집·로그
- `helm/loki/` — Loki
- `helm/tempo/` — Tempo
- `helm/yace/` — YACE (CloudWatch exporter)

### kustomize/

팀이 관리하는 운영 매니페스트.

- `kustomize/base/apps/aiops/` — AIOps 앱
- `kustomize/base/monitors/` — ServiceMonitor + metrics Service
- `kustomize/base/exporters/` — mysqld / Redis 등 exporter
- `kustomize/base/grafana/` — Grafana 대시보드 ConfigMap
- `kustomize/base/prometheusrule/` — PrometheusRule (레이어 알림)
- `kustomize/overlays/prod/` — 프로덕션 overlay

### infra/

클러스터 단위 설정. Kustomize overlay 적용 경로와 별도로 수동 또는 별 파이프라인으로 적용합니다.

- `infra/alertmanager/` — AlertmanagerConfig
- `infra/externalsecrets/` — SecretStore + ExternalSecret
- `infra/iam/` — IAM 정책·신뢰 정책 참고 파일
- `infra/lbc/`, `infra/loki/`, `infra/tempo/`, `infra/yace/` — IRSA 등 신뢰 정책

적용 예:

```bash
kubectl apply -f infra/alertmanager/
kubectl apply -f infra/externalsecrets/observability/
```

### archive/

루트에 두기 부적절한 대시보드/규칙 JSON·YAML 스냅샷 등을 보관합니다. 배포 소스 오브 트루스는 아닙니다.

## 운영 규칙

- **Helm** = 서드파티 컴포넌트 설치
- **Kustomize** = 운영 리소스(앱, 모니터, 룰, 대시보드)
- **infra** = 클러스터 인프라, 수동 적용
- **origins / 루트 레거시 디렉터리** = 참고·아카이브

## Apply (참고)

### 운영 리소스

```bash
kubectl apply -k kustomize/overlays/prod
```

### 클러스터 인프라 (수동)

```bash
kubectl apply -f infra/alertmanager/
kubectl apply -f infra/externalsecrets/observability/
```

## Secrets (로컬)

- 실제 `.env` 파일은 로컬 전용입니다.
- Secrets Manager + ExternalSecret으로 운영합니다.
- `kustomize/overlays/prod/`의 secretGenerator는 로컬 `.env`를 사용합니다(커밋하지 않음).
