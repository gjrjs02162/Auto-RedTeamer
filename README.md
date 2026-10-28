# Auto-RedTeamer: LLM 자동화 취약점 진단 프레임워크

## 1. 프로젝트 개요 (Project Overview)
**Auto-RedTeamer**는 인공지능(LLM) 기반의 애플리케이션 및 에이전트를 대상으로 보안 취약점을 자동으로 진단하는 'AI 레드팀(Red Team)' 프레임워크입니다. 
최근 대두되는 **OWASP Top 10 for LLM**의 핵심 위협인 프롬프트 인젝션(Prompt Injection)과 민감 정보 노출(Sensitive Information Disclosure) 취약점을 AI가 직접 공격하고 평가(Scoring)하여, 시스템의 안정성을 프로그래밍적으로 검증하는 것을 목표로 합니다.

## 2. 시스템 아키텍처 (System Architecture)
본 프로젝트는 3개의 독립적인 AI 에이전트(3-Agent Model)가 상호작용하는 구조로 설계되었습니다.

1. **Target Agent (방어자 / 타겟)**
   - 보안 규칙(System Prompt)이 적용된 테스트 대상 챗봇 또는 AI 에이전트.
   - 예: "사내 기밀 정보나 관리자 비밀번호를 절대 유출해서는 안 됨."
2. **Attacker Agent (공격자)**
   - 타겟의 방어벽을 우회하기 위해 Jailbreak, 사회공학적 기법이 적용된 변형 프롬프트를 무한히 생성하고 주입하는 역할.
3. **Judge Agent (평가자)**
   - 타겟 에이전트의 최종 응답을 분석하여, 실제로 취약점이 노출되었는지(방어 실패/성공)를 객관적으로 평가하고 로깅하는 역할.

## 3. 데이터 플로우 (Data Flow)
1. **[Payload 생성]**: Attacker Agent가 공격 시나리오(Jailbreak 등)를 바탕으로 악성 프롬프트를 생성.
2. **[공격 주입]**: 생성된 프롬프트를 API를 통해 Target Agent에게 전송.
3. **[응답 수신]**: Target Agent가 공격 방어에 성공했거나 실패한 텍스트 응답을 반환.
4. **[보안 평가]**: Target의 응답 텍스트를 Judge Agent에게 전송하여 분석 요청.
5. **[결과 로깅]**: Judge Agent가 판별한 결과(Pass/Fail)와 취약점 유형을 데이터베이스(CSV/JSON)에 기록.

## 4. 기술 스택 (Tech Stack)
- **Language:** Python 3.x
- **LLM API:** OpenAI API / Gemini API (또는 로컬 LLM 환경)
- **Libraries:** 
  - `requests`, `aiohttp` (비동기 API 통신)
  - `pandas` (평가 결과 데이터 처리 및 리포팅)
  - `python-dotenv` (API Key 등 환경 변수 관리)

## 5. 프로젝트 디렉토리 구조 (Directory Structure)
```text
auto-redteamer/
│
├── src/
│   ├── agents/
│   │   ├── target_agent.py    # 타겟 AI 구현부
│   │   ├── attacker_agent.py  # 공격자 AI 구현부
│   │   └── judge_agent.py     # 평가자 AI 구현부
│   ├── core/
│   │   └── orchestrator.py    # 3개 에이전트 간의 통신 및 파이프라인 제어
│   └── utils/
│       └── logger.py          # 평가 결과 및 로그 저장
│
├── data/
│   ├── base_prompts.json      # 공격 시나리오 초기 데이터셋
│   └── reports/               # 분석 결과 리포트 (CSV, HTML) 저장 위치
│
├── .env.example               # 환경 변수(API Key) 템플릿
├── requirements.txt           # 필요 파이썬 패키지 목록
├── main.py                    # 프레임워크 실행 엔트리 포인트
└── README.md                  # 프로젝트 설명서 (현재 문서)