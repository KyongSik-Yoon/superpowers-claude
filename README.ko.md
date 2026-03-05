# Superpowers (Claude Code Fork)

> **[obra/superpowers](https://github.com/obra/superpowers)의 포크** — Claude Code 전용으로 경량화.
> 업스트림 버전: v4.3.1. Cursor, Codex, OpenCode가 필요하다면 원본을 사용하세요.

[English](README.md)

Superpowers는 Claude Code를 위한 완전한 소프트웨어 개발 워크플로우입니다. 조합 가능한 "스킬"과 에이전트가 이를 활용하도록 하는 초기 지침으로 구성되어 있습니다.

## 이 포크의 차이점

Claude Code 전용으로 특화하여 다음과 같이 변경했습니다:

**제거됨 (Claude Code 전용화)**
- Cursor 플러그인 (`.cursor-plugin/`), Codex 지원 (`.codex/`), OpenCode 플러그인 (`.opencode/`) — 모든 비-Claude 플랫폼 제거
- `lib/skills-core.js` (OpenCode 공유 모듈), 플랫폼별 문서 및 테스트 스위트
- SessionStart 훅 간소화 — Cursor `additional_context` 필드 제거

**추가됨**
- **dynamic-model-selection** 스킬 — 작업 복잡도에 따라 서브에이전트를 적절한 모델 티어(opus/sonnet/haiku)로 라우팅하여 품질을 유지하면서 비용 약 40% 절감
- **GitHub Actions 릴리스 워크플로우** — 태그 푸시 시 `RELEASE-NOTES.md`로부터 자동 GitHub Release 생성

## 작동 방식

코딩 에이전트를 실행하는 순간부터 시작됩니다. 무언가를 만들려 한다는 것을 감지하면, 바로 코드를 작성하지 *않습니다*. 대신 한 발 물러서서 실제로 무엇을 하려는지 물어봅니다.

대화를 통해 스펙을 도출한 뒤, 실제로 읽고 소화할 수 있을 만큼 짧은 단위로 나누어 보여줍니다.

설계를 승인하면, 에이전트는 열정적이지만 경험이 부족한 주니어 엔지니어도 따라할 수 있을 만큼 명확한 구현 계획을 작성합니다. 진정한 RED-GREEN TDD, YAGNI(You Aren't Gonna Need It), DRY를 강조합니다.

"시작"이라고 하면, *서브에이전트 주도 개발* 프로세스를 실행하여 각 엔지니어링 작업을 에이전트가 처리하고, 결과를 검사하고 리뷰한 후 계속 진행합니다. Claude가 계획에서 벗어나지 않고 몇 시간 동안 자율적으로 작업하는 것도 흔한 일입니다.

이것이 시스템의 핵심이며, 스킬이 자동으로 트리거되기 때문에 특별한 작업을 할 필요가 없습니다. 코딩 에이전트가 그냥 Superpowers를 갖게 됩니다.

## 설치

Claude Code에서 마켓플레이스를 먼저 등록합니다:

```bash
/plugin marketplace add KyongSik-Yoon/superpowers-claude
```

그런 다음 플러그인을 설치합니다:

```bash
/plugin install superpowers@superpowers-claude
```

### 설치 확인

새 세션을 시작하고 스킬을 트리거할 만한 요청을 합니다 (예: "이 기능을 설계해줘" 또는 "이 버그를 디버깅하자"). 에이전트가 자동으로 관련 superpowers 스킬을 호출해야 합니다.

## 기본 워크플로우

1. **brainstorming** - 코드 작성 전에 활성화. 질문을 통해 아이디어를 다듬고, 대안을 탐색하고, 설계를 섹션별로 검증합니다. 설계 문서를 저장합니다.

2. **using-git-worktrees** - 설계 승인 후 활성화. 새 브랜치에 격리된 작업 공간을 만들고, 프로젝트를 셋업하고, 클린한 테스트 기준선을 확인합니다.

3. **writing-plans** - 승인된 설계로 활성화. 작업을 작은 단위(각 2-5분)로 분할합니다. 모든 작업에 정확한 파일 경로, 완전한 코드, 검증 단계가 포함됩니다.

4. **subagent-driven-development** 또는 **executing-plans** - 계획으로 활성화. 작업당 새로운 서브에이전트를 배치하여 2단계 리뷰(스펙 준수, 코드 품질)를 수행하거나, 배치 실행으로 중간 체크포인트를 둡니다.

5. **test-driven-development** - 구현 중 활성화. RED-GREEN-REFACTOR를 강제합니다: 실패하는 테스트 작성, 실패 확인, 최소 코드 작성, 통과 확인, 커밋. 테스트 전에 작성된 코드는 삭제합니다.

6. **requesting-code-review** - 작업 사이에 활성화. 계획 대비 리뷰하고, 심각도별로 이슈를 보고합니다. 치명적 이슈는 진행을 차단합니다.

7. **finishing-a-development-branch** - 작업 완료 시 활성화. 테스트를 확인하고, 옵션(머지/PR/유지/폐기)을 제시하고, 워크트리를 정리합니다.

**에이전트는 모든 작업 전에 관련 스킬을 확인합니다.** 제안이 아닌 필수 워크플로우입니다.

## 스킬 라이브러리

**테스팅**
- **test-driven-development** - RED-GREEN-REFACTOR 사이클 (테스팅 안티패턴 참조 포함)

**디버깅**
- **systematic-debugging** - 4단계 근본 원인 분석 프로세스 (root-cause-tracing, defense-in-depth, condition-based-waiting 기법 포함)
- **verification-before-completion** - 실제로 수정되었는지 확인

**협업**
- **brainstorming** - 소크라틱 설계 정제
- **writing-plans** - 상세 구현 계획
- **executing-plans** - 체크포인트와 함께 배치 실행
- **dispatching-parallel-agents** - 동시 서브에이전트 워크플로우
- **requesting-code-review** - 사전 리뷰 체크리스트
- **receiving-code-review** - 피드백 대응
- **using-git-worktrees** - 병렬 개발 브랜치
- **finishing-a-development-branch** - 머지/PR 결정 워크플로우
- **subagent-driven-development** - 2단계 리뷰를 통한 빠른 반복 (스펙 준수, 코드 품질)

**비용 최적화**
- **dynamic-model-selection** - 작업 복잡도에 따라 모델 티어(opus/sonnet/haiku)를 매칭하여 비용 효율적인 서브에이전트 배치

**메타**
- **writing-skills** - 모범 사례에 따른 새 스킬 작성 (테스팅 방법론 포함)
- **using-superpowers** - 스킬 시스템 소개

## 철학

- **테스트 주도 개발** - 항상 테스트를 먼저 작성
- **체계적 접근** - 추측이 아닌 프로세스
- **복잡도 감소** - 단순함이 최우선 목표
- **증거 기반** - 성공을 선언하기 전에 검증

더 읽기: [Superpowers for Claude Code](https://blog.fsck.com/2025/10/09/superpowers/)

## 릴리스

GitHub Actions를 통해 자동화되어 있습니다. 버전 태그를 푸시하면 릴리스가 생성됩니다:

```bash
git tag v0.1.0
git push origin v0.1.0
```

워크플로우가 `RELEASE-NOTES.md` 내용으로 GitHub Release를 생성합니다.

## 기여

스킬은 이 저장소에 직접 포함되어 있습니다. 기여하려면:

1. 저장소를 포크합니다
2. 스킬을 위한 브랜치를 생성합니다
3. `writing-skills` 스킬의 가이드를 따라 새 스킬을 작성합니다
4. PR을 제출합니다

자세한 가이드는 `skills/writing-skills/SKILL.md`를 참조하세요.

## 업데이트

플러그인을 업데이트하면 스킬도 자동으로 업데이트됩니다:

```bash
/plugin update superpowers
```

## 라이선스

MIT License - LICENSE 파일을 참조하세요.

## 감사의 말

원본 Superpowers: [Jesse Vincent](https://github.com/obra) — [obra/superpowers](https://github.com/obra/superpowers). 유용하셨다면 [그의 오픈소스 활동 후원](https://github.com/sponsors/obra)을 고려해 주세요.

## 지원

- **이슈**: https://github.com/KyongSik-Yoon/superpowers-claude/issues
- **업스트림**: https://github.com/obra/superpowers
