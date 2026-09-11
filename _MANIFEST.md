# Project Manifest — Weekly_PixelartTutorials

## Repo

Weekly archive of 「教你画像素画」公众号 tutorials: one markdown issue per week under `doc/`, indexed by `README.md`.

## Layout

| Path | Role |
|------|------|
| `README.md` | Year/month index of all issues |
| `doc/issue-XXX.md` | Single weekly issue (tutorials + 鸡汤摘录) |
| `.claude/skills/update-issue-md/` | Local agent skill for creating/updating issues (gitignored with `.claude`) |

## Tier 1 (read first)

1. `README.md` — latest issue number and index style
2. Latest `doc/issue-*.md` — format template
3. `.claude/skills/update-issue-md/SKILL.md` — end-to-end update workflow (also mirrored at `~/.cursor/skills/update-issue-md/`)

## Agent workflow: 更新 issue

Trigger: user pastes WeChat URLs and/or says 更新issue / 完善 issue, often with a 鸡汤 line.

1. Create or fill next `doc/issue-XXX.md` (title + link + one-line summary per article).
2. Update `README.md` month index.
3. **Commit and push immediately. Do not ask whether to push** (unless user said not to).

Conflict priority: this manifest + `update-issue-md` skill over ad-hoc “ask before push” habits for this task.
