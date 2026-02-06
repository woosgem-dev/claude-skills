# Claude Skills Collection

A collection of reusable Claude Code skills for development best practices and workflows. These are practical, reference-quality guides that Claude can use to provide consistent, high-quality assistance.

## Skills

| Skill | Description |
|-------|-------------|
| [typescript-conventions](skills/typescript-conventions.md) | TypeScript type system, generics, strict config, error handling, naming conventions, utility types |
| [code-review-checklist](skills/code-review-checklist.md) | Systematic code review process covering correctness, security, performance, testing, and maintainability |
| [git-workflow](skills/git-workflow.md) | Branch strategy, commit conventions (Conventional Commits), PR workflow, release process, and Git hooks |

## Usage with Remote Skill Registry

These skills are designed to be fetched by the [remote-skill-registry](https://github.com/woosgem-dev/remote-skill-registry) MCP server. Configure your `skills-config.json` to point at this repository:

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

## Adding New Skills

1. Create a markdown file in the `skills/` directory
2. Follow the existing format: title, sections with practical examples
3. Add the skill entry to `skills-config.json`
4. Commit and push

## License

MIT
