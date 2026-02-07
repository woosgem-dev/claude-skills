# Claude Skills Collection

한국어 | **[English](README.md)**

개발 모범 사례와 워크플로우를 위한 재사용 가능한 Claude Code 스킬 모음입니다. Markdown 기반 스킬들은 일관되고 고품질의 가이드를 제공하며, [Remote Skill Registry](https://github.com/woosgem-dev/remote-skill-registry) MCP 서버를 통해 동적으로 로드됩니다.

---

## 사용 가능한 스킬

| 스킬 | 설명 | 태그 |
|------|------|------|
| [typescript-conventions](skills/typescript-conventions.md) | TypeScript 타입 시스템, 제네릭, strict 설정, 에러 처리, 네이밍 컨벤션, 유틸리티 타입 | `typescript`, `conventions`, `best-practices` |
| [code-review-checklist](skills/code-review-checklist.md) | 정확성, 보안, 성능, 테스트, 유지보수성을 다루는 체계적인 코드 리뷰 프로세스 | `code-review`, `quality`, `checklist` |
| [git-workflow](skills/git-workflow.md) | 브랜치 전략, Conventional Commits, PR 워크플로우, 릴리스 프로세스, Git 훅 | `git`, `workflow`, `version-control` |

---

## Remote Skill Registry와 함께 사용하기

이 스킬들은 [Remote Skill Registry](https://github.com/woosgem-dev/remote-skill-registry) MCP 서버를 통해 가져오도록 설계되었습니다.

### 설정 예제

`skills-config.json`에 스킬을 추가하세요.

```json
{
  "version": "1.0.0",
  "skills": [
    {
      "path": "https://github.com/woosgem-dev/claude-skills/blob/main/skills/typescript-conventions.md",
      "name": "typescript-conventions",
      "description": "TypeScript 모범 사례 및 컨벤션",
      "tags": ["typescript", "conventions"],
      "auth": "none"
    },
    {
      "path": "https://github.com/woosgem-dev/claude-skills/blob/main/skills/code-review-checklist.md",
      "name": "code-review-checklist",
      "description": "코드 리뷰 가이드라인 및 체크리스트",
      "tags": ["code-review", "quality"],
      "auth": "none"
    },
    {
      "path": "https://github.com/woosgem-dev/claude-skills/blob/main/skills/git-workflow.md",
      "name": "git-workflow",
      "description": "Git 워크플로우 및 커밋 컨벤션",
      "tags": ["git", "workflow"],
      "auth": "none"
    }
  ]
}
```

### MCP 서버 명령어

```bash
# 모든 스킬 목록 조회
claude skills list

# 특정 스킬 가져오기
claude skills get typescript-conventions

# 태그로 검색
claude skills search --tag typescript
```

---

## Tournament System 통합

스킬에 YAML frontmatter를 추가하면 [Skill Tournament System](https://github.com/woosgem-dev/remote-skill-registry)의 검색 엔진이 활용할 수 있습니다. 이를 통해 경쟁 기반 스킬 선택 및 성능 기반 랭킹이 가능합니다.

### Frontmatter 형식

```yaml
---
id: "typescript-conventions"
title: "TypeScript Conventions"
description: "TypeScript 모범 사례: 타입 시스템, 제네릭, strict 설정, 에러 처리, 네이밍 컨벤션, 유틸리티 타입"
tags:
  - typescript
  - conventions
  - best-practices
version: "1.0.0"
location: "https://github.com/woosgem-dev/claude-skills/blob/main/skills/typescript-conventions.md"
author: "woosgem-dev"
created: "2026-02-07"
updated: "2026-02-07"
---
```

### Tournament 기능

- **스킬 검색**: 태그 및 메타데이터를 통해 자동으로 스킬 인덱싱
- **경쟁 기반 선택**: 토너먼트 방식으로 스킬이 경쟁하여 선택됨
- **성능 추적**: 다양한 맥락에서 어떤 스킬이 가장 효과적인지 추적
- **동적 로딩**: 우승 스킬을 필요 시 동적으로 로드

---

## 새로운 스킬 추가하기

1. **스킬 파일 생성**
   ```bash
   touch skills/your-skill-name.md
   ```

2. **(선택) YAML Frontmatter 추가**
   - `id`, `title`, `description`, `tags`, `version`, `location`, `author`, `created`, `updated` 포함
   - Frontmatter를 추가하면 토너먼트 검색 기능 활성화

3. **설정 업데이트**
   - Remote Skill Registry의 `skills-config.json`에 스킬 항목 추가

4. **커밋 및 푸시**
   ```bash
   git add skills/your-skill-name.md
   git commit -m "feat: add your-skill-name skill"
   git push origin main
   ```

### 스킬 작성 가이드라인

- **제목**: 명확하고 행동 지향적 (`TypeScript Conventions`, `TS 가이드` 지양)
- **구조**: 헤더(`##`, `###`), 테이블, 코드 블록 활용
- **예제**: 실용적이고 복사-붙여넣기 가능한 코드 포함
- **길이**: 튜토리얼이 아닌 레퍼런스 품질 지향 (100-300줄)
- **태그**: 구체적이고 검색 가능한 태그 사용 (`typescript`, `programming` 지양)

---

## 저장소 구조

```
claude-skills/
├── README.md              # 영문 문서
├── README.ko.md           # 한글 문서
└── skills/
    ├── typescript-conventions.md
    ├── code-review-checklist.md
    └── git-workflow.md
```

---

## 관련 프로젝트

- [Remote Skill Registry](https://github.com/woosgem-dev/remote-skill-registry) - Skill Tournament System이 포함된 MCP 서버
- [WooSGem Design System](https://github.com/woosgem-dev/woosgem) - 멀티 프레임워크 디자인 시스템 모노레포

---

## 라이선스

MIT © woosgem-dev
