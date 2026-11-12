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

## 프로젝트 등록과 실행

이 저장소를 공통 운영 루트로 열고 `projects.example.yaml`을 `projects.local.yaml`로 복사해 프로젝트별 `name`, `aliases`, 절대 `path`를 등록한다. 로컬 파일은 `.gitignore`에 포함하며 비밀값을 저장하지 않는다. 예시에는 실제 로컬 경로를 넣지 않는다. 프로젝트마다 대상 루트에 `ai-dlc.yaml`이 있어야 한다. 새 설정은 `ai-dlc.example.yaml`을 참고해 작성하며, 이미 존재하는 설정과 작업 기록은 덮어쓰지 않는다.

예를 들어 `Ask-Teacher 프로젝트 작업 시작하자`라고 요청하면 Main은 등록 이름·별칭으로 대상을 유일하게 식별하고 대상의 `ai-dlc.yaml`을 읽는다. 등록이 없거나 중복되면 사용자에게 묻는다. 실제 Git 루트·원격과 설정 일치, GitHub Project·명세·테스트 접근을 확인하며 불명확한 경우 중단한다. 자세한 절차는 공통 `AGENTS.md`를 따른다.

공통 스킬·에이전트·리뷰 기준·템플릿은 이 저장소에서 읽는다. 프로젝트 설정, 소스, `docs/current-task.md`, `docs/current-work-log.md`, 월별 devlog, Git 및 테스트는 대상 프로젝트에서 처리한다. `.github` 템플릿은 대상 파일을 우선 사용하고 없으면 공통 템플릿을 참고한다. 기존 프로젝트 파일은 자동 복사하거나 삭제하지 않는다.

이 구조는 문서에 명시된 운영 절차다. Codex 실행 환경이 공통 스킬과 사용자 정의 역할을 실제로 발견·호출하는지 확인해야 하며, 자동 로딩이나 기존 세션의 즉시 갱신을 보장하지 않는다. 사용할 수 없으면 필수 역할을 생략하지 않고 중단해 보고한다. 환경의 파일 접근 권한과 보호 규칙도 별도로 적용된다.

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
- 공식 요구사항·API·인증·ERD·UI 등은 설정된 Specs 원본을 읽기 전용으로 참조한다. 일반 개발에서 공식 계약과 AI-DLC 운영 파일을 수정하지 않는다. README는 승인된 작업 범위에서 수정할 수 있으며 운영 규칙·승인 조건·보호 파일의 수정 제한을 변경하거나 우회하는 데 사용하지 않는다.
- 설계·작업 로그·devlog·Skill이 명시한 상태 파일은 기존 역할과 승인 범위에서 수정한다. 파일별 권한과 예외는 `AGENTS.md`를 따른다.
- 계약 변경 필요 시 기존 계약을 지키는 대안을 먼저 검토한다. Main은 사유·영향·수정안과 검토 근거를 사용자에게 보고하고 관련 Issue·PR에 기존 승인 범위에서 기록하며, 영향받는 작업은 별도 계약 관리 결과 확인과 재검증까지 보류한다.
- 테스트·CI·리뷰에서 확인하지 않은 결과를 성공으로 보고하지 않는다.
- PR Merge와 중요한 의사결정은 사용자가 담당한다.
- 토큰·비밀번호·API 키 등 비밀값은 설정 및 작업 기록에 포함하지 않는다.

## 공통 AI-DLC 업데이트

공통 운영 파일 변경은 사용자가 승인한 별도 AI-DLC 유지보수에서 지정된 범위만 수행한다. 기존 승인·역할·품질 게이트와 사용자 직접 merge 원칙을 유지하고, 실제 diff와 승인 범위를 대조한다. 공식 계약 변경은 별도 절차를 따른다.

프로젝트마다 복사된 기존 운영 파일은 공통 경로와의 차이 및 실제 로딩을 검증하기 전까지 삭제하지 않는다. 프로젝트별 설정과 실제 작업 기록도 보존한다. 공통 저장소의 유지보수 이력과 검증은 PR에서 관리하며 배포용 `docs/current-task.md`와 `docs/current-work-log.md`에는 실제 작업 기록을 넣지 않는다.
