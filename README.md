<div align="center">

# 김현민 (Hyeonmin Kim)

**SRE · DevOps · AWS**

운영이 쉬운 시스템을 만들고 싶어서 이 일을 합니다.  
배포가 무서운 팀, 장애 원인을 못 찾는 팀, 반복 작업에 지친 팀에서  
그 문제를 직접 해결하는 쪽을 선택해왔습니다.

<p>
  <img src="https://img.shields.io/badge/AWS-232F3E?style=flat-square&logo=amazonaws&logoColor=white" />
  <img src="https://img.shields.io/badge/ECS-FF9900?style=flat-square&logo=amazon-ecs&logoColor=white" />
  <img src="https://img.shields.io/badge/Terraform-844FBA?style=flat-square&logo=terraform&logoColor=white" />
  <img src="https://img.shields.io/badge/Datadog-632CA6?style=flat-square&logo=datadog&logoColor=white" />
  <img src="https://img.shields.io/badge/Grafana-F46800?style=flat-square&logo=grafana&logoColor=white" />
  <img src="https://img.shields.io/badge/Jenkins-D24939?style=flat-square&logo=jenkins&logoColor=white" />
  <img src="https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white" />
  <img src="https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white" />
  <img src="https://img.shields.io/badge/Bedrock-0B5FFF?style=flat-square&logo=amazonaws&logoColor=white" />
  <img src="https://img.shields.io/badge/CKA_준비중-326CE5?style=flat-square&logo=kubernetes&logoColor=white" />
</p>

</div>

---

## 어떤 일을 해왔는지

SM Entertainment 계열사 디어유(DearU)에서 유료 가입자 250만 규모의 프라이빗 메신저 서비스 **BUBBLE**의 AWS 인프라 전반을 단독으로 맡았습니다. 입사 당시 퍼블릭 인스턴스 기반 구조였던 걸 지금의 ECS 컨테이너 환경으로 바꾸는 일부터, 멀티 계정 5개를 관리하는 내부 콘솔 개발까지 설계하고 운영했습니다.

규모가 커지면서 생긴 문제들 — 배포가 불안해서 야간 호출이 잦았던 것, MSP가 장애 원인을 못 찾던 것, 로그가 흩어져서 개발팀이 직접 조회하지 못했던 것 — 을 하나씩 없애는 데 집중했습니다.

---

## 주요 기술 스택

### Cloud & Infra
AWS (ECS · ECR · EC2 · Lambda · ASG · S3 · RDS · OpenSearch · DynamoDB · ElastiCache · Athena · EventBridge · SES · Bedrock)  
VPC · ALB · CloudFront · API Gateway · Route53 · Transit Gateway · PrivateLink · Palo Alto · VGW · OpenVPN AS

### Observability
Datadog (APM / Infra / Log) · Grafana · Loki · Promtail · Prometheus → CloudWatch 전환 · ELK + Kafka · Filebeat · Packetbeat · Athena 기반 로그 분석

### Automation & IaC
Terraform · Jenkins · GitHub Actions · ArgoCD · Ansible · Semaphore · Boto3 · Shell

### Security & Compliance
Keycloak + SAML SSO · Active Directory · IAM RBAC · CloudTrail · SonarQube · ISMS-P (4년 연속 심사 담당)

### AI & ML
AWS Bedrock · OpenSearch Vector Search · RAG · LLM (건국대 대학원 AI 전공 연계)

---

## 실제로 한 것들

### 서비스 구조 전환
단일 인스턴스 기반의 서비스를 ECR/ECS 컨테이너 환경으로 재구성했습니다. dev / QA / stg / live 네 단계 배포 환경을 나누고, 스테이지 간 통신을 차단하는 보안 구조를 만들었습니다. 인스턴스를 Private Subnet으로 옮기고 Proxy Layer를 두어 3-Tier 구조를 완성했으며, Auto Scaling Group + CloudWatch 기반 자동 확장에 롤링 업데이트를 적용해 배포 중 다운타임을 없앴습니다.

### WEB/WAS 분리 및 MSA 전환
기존 Nginx + Python/Java 혼재 모놀리식 구조에서 WEB과 WAS를 분리하는 프로젝트를 주도했습니다. 세션 발표로 내부 설득을 이끌었고, Static 자원을 분리해 CloudFront CDN 캐싱을 적용했습니다. Route53 가중치 라우팅 (99:1 → 70:30 → 40:60 → 0:100)으로 사용자 영향 없이 무중단 전환했습니다.

### Observability 체계 구축
New Relic · Splunk · Datadog 3사를 라이브 서비스 5% 트래픽으로 PoC를 진행해 Datadog을 직접 선정했습니다. 이후 에이전트 필터링, 정상 로그 10% 샘플링, Index 차등화, S3/Glacier Archive 전략으로 **Datadog 월 비용 약 30% 절감**했습니다. 초기엔 Prometheus로 시작했는데 트래픽 피크 시 에이전트 리소스 부담이 커서 CloudWatch로 재정비했습니다.

Loki + Promtail 기반 로그 대시보드는 개발자가 Stage / 서비스 / 프로젝트 변수와 키워드 검색으로 직접 조회하도록 설계했고, Ansible 플레이북으로 Promtail 설정을 일괄 동기화해서 새 서비스 추가 시 수동 작업을 없앴습니다.

### ELK + Kafka 로그 파이프라인
일일 수억 라인 규모의 로그를 처리하는 파이프라인을 구성했습니다. Filebeat·Packetbeat → Kafka → Logstash(Grok 필터) → OpenSearch 순서로 연결했고, OpenSearch가 다운돼도 Kafka 버퍼로 로그 손실 없이 처리됩니다. 라이브 방송 사용자 메시지를 실시간 분석해 Kibana로 시각화하고, Anomaly Detection과 Script Field 기반 알럿으로 아티스트 메시지 감지 체계도 만들었습니다.

### 운영 자동화 — Slack 봇 "Bubby"
인프라 이슈 수동 확인에 들어가던 시간이 너무 길었습니다. AWS Bedrock 에이전트에 SRE 업무 페르소나를 부여하고, Action Group과 Lambda를 연동해서 Slack에서 자연어로 인프라 상태를 조회하거나 리소스를 제어할 수 있도록 만들었습니다. Slack 메시지 · 운영 문서 · 인프라 정보를 OpenSearch Vector DB에 인덱싱해 RAG 구조로 연결했고, Slack 3초 응답 제한을 우회하기 위해 Dispatcher-Worker 비동기 처리 구조로 설계했습니다.

### 멀티 계정 통합 관리 콘솔 — DearU Console
5개 AWS 계정에 분산된 EC2 · RDS · ECS · S3 · Lambda 자원을 한 화면에서 볼 수 있는 내부 콘솔을 만들었습니다. Flask · WebSocket · Athena · LDAP 기반으로 구성했고, CloudTrail 이벤트를 분석해 이상 행위 탐지와 위험도 판단 기능도 붙였습니다.

### 배포 자동화 및 CI/CD
Boto3로 ECR · ECS · ASG · ELB를 순차 자동 생성하는 풀스택 프로비저닝 스크립트를 만들었습니다. Jenkins Slave를 병렬 구성하고 EFS + lsyncd로 빌드 데이터를 동기화해 빌드 대기시간을 줄였습니다. GitLab Runner에서 GitHub Actions로 마이그레이션했고, PR 기반 승인 체계와 SonarQube 코드 분석을 CI에 연결했습니다. API Gateway 솔루션을 Traefik · AWS API Gateway · Kong 세 가지로 비교해보고 Kong을 관리용 Proxy로 채택했습니다. JMeter로 Django uWSGI의 worker 수, timeout, 동시 처리 요청 수를 반복 테스트해 최적값을 도출한 결과 **에러율 20% 이상 감소**했습니다.

### 인증 및 접근 통제
OpenVPN Access Server를 도입해 기존 프로파일 기반 접근을 Office + AD 인증으로 바꿨습니다. AD 비밀번호 만료 7일 전 SES로 자동 알림을 보내는 배치 스크립트를 만들었고, 셀프 서비스 비밀번호 변경 · 계정 잠금 해제, Chatbot 기반 FAQ를 담은 AD 관리 콘솔 웹 페이지도 구축했습니다. Keycloak + SAML로 Datadog · GitHub · SonarQube 통합 SSO를 구현했고, 코드에 하드코딩되던 IAM Access Key를 Role 기반으로 전환했습니다.

### 보안 (ISMS-P 4년 연속)
2022 · 2023 · 2024 · 2025년 ISMS-P 인증 심사를 메인 담당자로 수행했습니다. VPC · VPN · Syslog · Application Log · Audit Log를 단일 S3 계정에 통합 저장하고 라이프사이클 정책을 수립했습니다. SG Outbound 세분화, CloudTrail → OpenSearch/Kibana 시각화, IAM Role 전환이 이 심사 준비 과정에서 같이 진행됐습니다.

### 온프레미스 → AWS 이전
사내 메신저 · 이메일 · SVN 등 관리용 서버를 AWS로 이전했습니다. Apache · PHP · MySQL · HMail 의존성을 정리하고 SSL은 ACM으로 교체했으며, SMTP 발송은 SES로 옮겼습니다.

### MSP 및 비용 관리
MSP 계약을 직접 주도하고 월간 비용 미팅으로 AWS · 상용 솔루션 비용을 지속 관리했습니다. RFI/RFP/PoC를 동일 조건으로 진행하고 임직원 체감 비교표를 만들어 솔루션을 선정하는 체계를 운영했습니다.

### 일본 오피스 기술 인수인계
도쿄 현지 개발·운영 조직을 대상으로 ECS MSA 아키텍처, IAM 권한 설계, Datadog · Grafana 운영 체계를 전달하고 운영 매뉴얼을 작성했습니다.

---

## 이전 경력 요약

**CDNetworks** (2021–2022) — WAF · DDoS · Bot 보호 솔루션 운영, CDN 아키텍처 설계, OTT 미디어 스트리밍 플랫폼(DRM) 구축. 청와대·서울시·호텔신라 등 고객사 보안 대응.

**두루엔케이션 / 오픈베이스** (2018–2021) — SBS I&M 인프라 혁신 TF 상주. IBM Power/AIX · Dell/HP · EMC Storage 멀티벤더 환경 운영. AWS 마이그레이션 참여. Zabbix · Grafana 모니터링 구축.

**메인아이티** (2013–2017) — 한국가스공사·농림축산검역본부·CITI은행 등 공공·금융 인프라 구축 및 유지보수. 망분리 프로젝트 PL. 통합 로그 관리 시스템 구축.

---

## 지금 하고 있는 것

- 건국대 정보통신대학원 인공지능전공 석사과정 재학 중 — Bedrock · LLM · RAG 실무 연계 연구
- **CKA (Certified Kubernetes Administrator) 취득 준비 중** — ECS 기반 운영 경험을 Kubernetes 환경으로 확장 중

---

## 일하는 방식

- 문제를 해결할 때 기능 구현보다 운영 프로세스 정립을 먼저 생각합니다. 시스템은 매뉴얼과 승인 체계까지 갖춰야 안정적이라고 봅니다.
- 도입 전 RFI/RFP/PoC를 거치고 동일 조건 비교표를 만든 뒤 선택합니다.
- 반복 작업은 스크립트와 플레이북으로 남겨서 다음 사람이 같은 일을 반복하지 않게 합니다.
- 모든 변경은 Git에 기록되어야 하고, 승인 체계 없이 단독 배포되지 않아야 한다고 생각합니다.

---

<div align="center">
<a href="mailto:babyoops00@naver.com">babyoops00@naver.com</a> · <a href="https://github.com/royalsaves">github.com/royalsaves</a>
</div>
