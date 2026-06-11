# Hi, I'm Juwon 👋

## 🛡️ About Me
- **Security Engineer** (Trainee)
- Focus on **Wazuh / SIEM / Web Hacking**
- Studying for **CPPG** & **Information Security Engineer**

## 🛠️ Skills
- **Infrastructure:** Linux, Docker, pfSense
- **Security Ops:** Wazuh, ELK Stack, Suricata
- **Offensive:** Burp Suite, sqlmap, Metasploit

---

## 📊 My Security Journey

| TryHackMe Profile |
| :---: |
| [TryHackMe Profile](https://tryhackme.com/p/JUWON) |


## 📁 Projects
- **ESTSOFT [8기] 인프라 보안 부트캠프 https://www.notion.so/oreumi/2b5ebaa8982b8021bf65c6ce1abc5237
- # 🧃 Juice Shop Attack & Detection Lab

> **AI-Powered SOC Automation** — 웹 공격 시뮬레이션부터 LLM 기반 자동 분석·알림까지 구현한 보안 자동화 실습 프로젝트

---

## 📌 프로젝트 개요

OWASP Juice Shop을 대상으로 실제 공격을 수행하고, Wazuh SIEM이 탐지한 로그를 **Ollama(Llama 3.2) LLM이 자동 분석**하여 Discord로 보안 리포트를 전송하는 **End-to-End SOC 자동화 파이프라인**을 구축했습니다.

```
공격 수행 → Wazuh 커스텀 룰 탐지 → Ollama LLM 분석 → Discord 알림
(Offensive)      (1차 필터)           (AI 해석)         (자동 리포트)
```

---

## 🏗️ 시스템 아키텍처

```
[VMware Ubuntu 22.04]
│
├── Docker Container 1 : OWASP Juice Shop   (공격 대상)
├── Docker Container 2 : Wazuh SIEM         (탐지 엔진)
├── Docker Container 3 : Ollama (Llama 3.2) (AI 분석)
└── Discord Webhook                          (알림 전송)
```

**환경**
- Host OS: VMware Workstation + Ubuntu 22.04
- 컨테이너 오케스트레이션: Docker

---

## 🛠️ 기술 스택

| 분류 | 기술 |
|---|---|
| 공격 시뮬레이션 | Burp Suite, sqlmap, 수동 IDOR 테스트 |
| SIEM / 탐지 | Wazuh, ELK Stack (Elasticsearch·Logstash·Kibana) |
| 네트워크 IDS | Suricata |
| AI 분석 엔진 | Ollama (Llama 3.2) — 로컬 LLM |
| 알림 연동 | Discord Webhook |
| 인프라 | Docker, Linux, VMware |

---

## 🎯 구현 기능

### 1. 커스텀 Wazuh 룰 작성
- IDOR (Insecure Direct Object Reference) 탐지 룰
- SQL Injection 패턴 탐지 룰
- Broken Access Control 이벤트 분류

### 2. LLM 자동 분석 파이프라인
Wazuh가 탐지한 로그를 Ollama에 전달하여 아래 항목을 자동 생성:
- 공격 유형 분류
- 위험도 점수 (1~10)
- 공격 의도 분석
- 대응 권고 사항

### 3. Discord 보안 리포트 자동 전송
```
🚨 보안 위협 분석 보고

공격 유형: SQL Injection
위험도: high
분석 내용: 로그인 시도에서 'OR 1=1'이 발견되었습니다.
권장 조치: 해당 로그인을 차단하고 보안 패치 적용 필요
원본 로그: Test Login Attempt: admin OR 1=1
```

---

## ⚠️ 한계점 및 개선 방향

> 구현 과정에서 **LLM 단독 의존 구조의 실질적 한계**를 직접 확인했습니다.

| 문제 | 원인 | 개선 방향 |
|---|---|---|
| 동일 공격에 위험도 6~9점으로 불일치 | LLM 출력 비결정성 | 프롬프트 구조화 + 출력 포맷 강제 |
| 고부하 시 타임아웃으로 탐지 누락 | 로컬 GPU 리소스 부족 | Regex 1차 필터로 LLM 호출 최소화 |
| 한·영·일 혼재 출력 | Llama 3.2 한국어 성능 한계 | 경량 모델 교체 또는 번역 후처리 |

**목표 개선 아키텍처 (Defense in Depth)**
```
Wazuh 탐지
    │
    ├─ [1차] Regex / 시그니처 기반 즉시 분류  ← 빠르고 가벼움
    │         (SQLi, XSS, IDOR 패턴 매칭)
    │
    └─ [2차] LLM 상세 분석                    ← 고위험 건만 호출
              (위험도 높은 이벤트만 선별 전송)
                  │
              Discord 리포트
```
> 이 구조는 **Safety와 Latency의 트레이드오프**를 해결하는 다층 방어(Defense in Depth) 패턴으로, 실제 AI 보안 파이프라인 설계에서 권장되는 방식입니다.

---

## 📂 디렉토리 구조

```
juice-shop-lab/
├── wazuh/
│   └── custom_rules.xml       # IDOR, SQLi 커스텀 탐지 룰
├── ollama/
│   └── analyzer.py            # LLM 분석 파이프라인
├── discord/
│   └── webhook_sender.py      # Discord 알림 모듈
└── docker-compose.yml         # 전체 환경 구성
```

---

## 🔍 탐지 시나리오

### Scenario 1. IDOR — Basket Access
```
공격: GET /rest/basket/1  (타 사용자 장바구니 무단 접근)
탐지: Wazuh Rule — Broken Access Control
분석: LLM이 IDOR 공격 의도 및 대응 방안 자동 생성
```

### Scenario 2. SQL Injection
```
공격: GET /login.php?user= OR 1=1 --
탐지: Wazuh Rule — SQL Injection Pattern
분석: LLM이 인증 우회 시도 탐지 및 패치 권고
```

---

## 💡 Key Takeaways

- **Offensive + Defensive** 양쪽 시각으로 동일 시스템을 분석하는 경험
- 로컬 LLM을 외부 API 대신 사용함으로써 **데이터 프라이버시** 보장 구조 설계
- 실제 운영 환경의 **리소스 제약과 정확도 트레이드오프** 문제를 직접 체험하고 개선 방향 도출

---

## 👤 Author

**Juwon** · Security Engineer (Trainee)  
[![GitHub](https://img.shields.io/badge/GitHub-jw1011-black?logo=github)](https://github.com/jw1011)  
[![TryHackMe](https://img.shields.io/badge/TryHackMe-Profile-red?logo=tryhackme)](https://tryhackme.com)

> 📜 Certifications: CPPG · 정보처리기사 (필기 합격) · Google Cloud AI Study Jam

![visitors](https://komarev.com/ghpvc/?username=jw1011&color=blue&style=flat-square&label=VISITORS)
