# ai-dlc-backend

**AI-DLC (AI-Driven Development Life Cycle)** 기반의 재사용 가능한 Backend 개발 자동화 환경이다.

GitHub Issue를 중심으로 AI 에이전트가 설계·구현·리뷰·기록을 수행하고, 사용자가 주요 의사결정과 PR Merge를 담당한다.

프로젝트별 연결 정보는 `ai-dlc.yaml`로 분리하여 공통 개발 워크플로우를 여러 Backend 프로젝트에 재사용할 수 있다.

## 주요 기능

- **Issue 기반 개발:** Issue 분석부터 설계, 구현, 리뷰, PR까지 단계별 진행
- **역할 분리:** Architect(설계·리뷰), Builder(구현·테스트), Reporter(기록)
- **품질 게이트:** 승인 범위, 테스트, 리뷰, CI 상태 확인
- **GitHub Projects 연동:** 작업 선정, 우선순위, 사용자 결정 및 진행 상태 관리
- **공식 명세 참조:** 요구사항·API·ERD의 원본을 프로젝트 설정에 따라 조회
- **개발 기록:** 작업 설계, 진행 로그 및 월별 devlog 관리
- **사용자 승인:** 중요한 결정과 PR Merge는 사용자가 직접 수행

## 디렉터리 구조

```text
ai-dlc-backend/
├── .agents/
│   └── skills/
│       ├── backlog-refinement/
│       ├── feature-discovery/
│       └── issue-driven-dev/
├── .codex/
│   └── agents/
│       ├── architect.toml
│       ├── builder.toml
│       └── reporter.toml
├── docs/
│   ├── devlog/
│   │   └── TEMPLATE.md
│   ├── prompts/
│   │   └── quick.md
│   ├── current-task.md
│   ├── current-work-log.md
│   └── review-checklist.md
├── templates/
│   └── .github/
│       ├── ISSUE_TEMPLATE/
│       └── PULL_REQUEST_TEMPLATE/
├── AGENTS.md
├── ai-dlc.example.yaml
└── README.md
```

## 새 프로젝트에 적용하기

### 1. 공통 파일 복사

Backend 프로젝트의 루트 디렉터리에 다음 파일과 디렉터리를 복사한다.

- `.agents/`
- `.codex/`
- `docs/`
- `AGENTS.md`

`templates/.github/`의 내용은 대상 프로젝트의 `.github/`에 복사한다.

기존 파일이 있다면 덮어쓰기 전에 변경 사항과 충돌 여부를 확인한다.

이 저장소의 `README.md`는 사용 설명서이므로 복사하지 않는다.

### 2. 프로젝트 설정

`ai-dlc.example.yaml`을 대상 프로젝트의 루트에 `ai-dlc.yaml`이라는 이름으로 복사한다.

파일 안의 주석을 참고해 다음 정보를 설정한다.

- 서비스 식별자와 GitHub 저장소
- GitHub Projects 및 필드·선택지
- Issue 라벨
- 공식 요구사항·API·ERD 경로
- 브랜치 규칙과 테스트 명령

`[변경 필요]` 항목은 실제 값으로 교체하고, `[확인 필요]` 항목은 실제 프로젝트 설정과 대조한다.

### 3. GitHub 환경 확인

- GitHub Project의 필드와 선택지가 설정값과 일치하는지 확인한다.
- Issue 라벨이 실제 저장소에 존재하는지 확인한다.
- 공식 명세 파일의 경로와 접근 권한을 확인한다.
- GitHub 접근 권한과 개발 환경의 테스트 명령을 확인한다.

### 4. AI-DLC 실행

Codex에서 대상 프로젝트를 열고 `docs/prompts/quick.md`를 참고하여 작업을 시작한다.

새 프로젝트의 첫 작업에서는 `docs/current-task.md`와 `docs/current-work-log.md`의 예시 내용을 실제 작업 기록으로 교체한다.

## 기본 개발 흐름

1. **작업 선정:** GitHub Issue 또는 사용자가 승인한 작업 확인
2. **설계:** Architect가 요구사항과 구현 범위를 분석하고 Main이 설계 게이트 확인
3. **구현:** Builder가 승인된 범위에서 코드 작성 및 테스트
4. **리뷰:** Architect가 구현 결과를 독립적으로 검토하고 필요한 수정 반영
5. **기록:** Reporter가 검증된 결과를 월별 devlog에 기록
6. **PR 확인:** Main이 CI·리뷰·완료 조건을 확인하고 사용자에게 보고
7. **Merge:** 사용자가 직접 PR을 병합하고 이후 브랜치 정리

세부 규칙과 예외·중단 조건은 `AGENTS.md` 및 각 Skill을 따른다.

## 운영 원칙

- 한 번에 하나의 Issue를 기준으로 작업한다. 예외는 사용자의 명시적 승인을 따른다.
- 승인된 작업 범위를 벗어나 임의로 구현하지 않는다.
- 공식 명세의 원본을 임의로 변경하거나 중복 관리하지 않는다.
- 테스트·CI·리뷰에서 확인하지 않은 결과를 성공으로 보고하지 않는다.
- PR Merge와 중요한 의사결정은 사용자가 담당한다.
- 토큰·비밀번호·API 키 등 비밀값은 설정 및 작업 기록에 포함하지 않는다.

## 공통 AI-DLC 업데이트

이 저장소를 수정해도 이미 적용한 프로젝트에 변경 사항이 자동 반영되지는 않는다.

공통 설정이나 Skill이 변경되면 대상 프로젝트와 차이를 비교하고 필요한 파일만 반영한다. 프로젝트별 설정인 `ai-dlc.yaml`과 실제 작업 기록은 임의로 덮어쓰지 않는다.