# AI-SKILLS

Personal Claude Code skills collection. These skills are loaded into Claude Code via user-level configuration (`~/.claude/skills/`).

## Skills

### `/akhtiyar`

Engineering leadership dashboard — Sentry bugs, Jira priorities, security audit, architecture compliance, team activity, production commits, branch activity, and Claude updates in one command.

**Usage:**

```
/akhtiyar                              # Full dashboard (default: today)
/akhtiyar today                        # Full dashboard for today
/akhtiyar weekly                       # Full dashboard for last 7 days
/akhtiyar fortnightly                  # Full dashboard for last 14 days
/akhtiyar monthly                      # Full dashboard for last 30 days
/akhtiyar sentry                       # Top 3 Sentry issues
/akhtiyar jira                         # Highest priority Jira items
/akhtiyar security                     # Security vulnerability audit
/akhtiyar architecture                 # Architecture compliance check
/akhtiyar prod-commits                 # Last 5 production commits
/akhtiyar branch-activity main weekly  # Branch activity
/akhtiyar claude-updates               # What's new in Claude
```

## Setup

Skills are symlinked into `~/.claude/skills/` so Claude Code auto-discovers them:

```bash
# Clone this repo
git clone https://github.com/rosanpoudel/AI-SKILLS.git ~/AI-SKILLS

# Symlink skills into Claude Code's user-level skills directory
mkdir -p ~/.claude/skills
ln -sf ~/AI-SKILLS/skills/akhtiyar ~/.claude/skills/akhtiyar
```
