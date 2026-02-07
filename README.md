# Claude Skills Collection

**[한국어](README.ko.md)** | English

A collection of reusable Claude Code skills for development best practices and workflows. These Markdown-based skills provide consistent, high-quality guidance and can be dynamically loaded by the [Remote Skill Registry](https://github.com/woosgem-dev/remote-skill-registry) MCP server.

---

## Available Skills

| Skill | Description | Tags |
|-------|-------------|------|
| [typescript-conventions](skills/typescript-conventions.md) | TypeScript type system, generics, strict config, error handling, naming conventions, utility types | `typescript`, `conventions`, `best-practices` |
| [code-review-checklist](skills/code-review-checklist.md) | Systematic code review process covering correctness, security, performance, testing, and maintainability | `code-review`, `quality`, `checklist` |
| [git-workflow](skills/git-workflow.md) | Branch strategy, Conventional Commits, PR workflow, release process, Git hooks | `git`, `workflow`, `version-control` |

---

## Usage with Remote Skill Registry

These skills are designed to be fetched by the [Remote Skill Registry](https://github.com/woosgem-dev/remote-skill-registry) MCP server.

### Configuration Example

Add skills to your `skills-config.json`:

```json
{
  "version": "1.0.0",
  "skills": [
    {
      "path": "https://github.com/woosgem-dev/claude-skills/blob/main/skills/typescript-conventions.md",
      "name": "typescript-conventions",
      "description": "TypeScript best practices and conventions",
      "tags": ["typescript", "conventions"],
      "auth": "none"
    },
    {
      "path": "https://github.com/woosgem-dev/claude-skills/blob/main/skills/code-review-checklist.md",
      "name": "code-review-checklist",
      "description": "Code review guidelines and checklist",
      "tags": ["code-review", "quality"],
      "auth": "none"
    },
    {
      "path": "https://github.com/woosgem-dev/claude-skills/blob/main/skills/git-workflow.md",
      "name": "git-workflow",
      "description": "Git workflow and commit conventions",
      "tags": ["git", "workflow"],
      "auth": "none"
    }
  ]
}
```

### MCP Server Commands

```bash
# List all available skills
claude skills list

# Get a specific skill
claude skills get typescript-conventions

# Search by tag
claude skills search --tag typescript
```

---

## Tournament System Integration

Skills can include YAML frontmatter for the [Skill Tournament System](https://github.com/woosgem-dev/remote-skill-registry)'s discovery engine. This enables competitive skill selection and performance-based ranking.

### Frontmatter Format

```yaml
---
id: "typescript-conventions"
title: "TypeScript Conventions"
description: "TypeScript best practices: type system, generics, strict config, error handling, naming conventions, utility types"
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

### Tournament Features

- **Skill Discovery**: Automatically index skills via tags and metadata
- **Competitive Selection**: Skills compete in tournaments for selection
- **Performance Tracking**: Track which skills perform best in different contexts
- **Dynamic Loading**: Load winning skills on-demand

---

## Adding New Skills

1. **Create Skill File**
   ```bash
   touch skills/your-skill-name.md
   ```

2. **(Optional) Add YAML Frontmatter**
   - Include `id`, `title`, `description`, `tags`, `version`, `location`, `author`, `created`, `updated`
   - Frontmatter enables tournament discovery

3. **Update Configuration**
   - Add the skill entry to the Remote Skill Registry's `skills-config.json`

4. **Commit and Push**
   ```bash
   git add skills/your-skill-name.md
   git commit -m "feat: add your-skill-name skill"
   git push origin main
   ```

### Skill Writing Guidelines

- **Title**: Clear, action-oriented (`TypeScript Conventions`, not `TS Guide`)
- **Structure**: Use headers (`##`, `###`), tables, code blocks
- **Examples**: Include practical, copy-pasteable code
- **Length**: Aim for reference quality, not tutorials (100-300 lines)
- **Tags**: Use specific, searchable tags (`typescript`, not `programming`)

---

## Repository Structure

```
claude-skills/
├── README.md              # English documentation
├── README.ko.md           # Korean documentation
└── skills/
    ├── typescript-conventions.md
    ├── code-review-checklist.md
    └── git-workflow.md
```

---

## Related Projects

- [Remote Skill Registry](https://github.com/woosgem-dev/remote-skill-registry) - MCP server with Skill Tournament System
- [WooSGem Design System](https://github.com/woosgem-dev/woosgem) - Multi-framework design system monorepo

---

## License

MIT © woosgem-dev
