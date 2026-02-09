# Blooperpowers

A fork of [superpowers](https://github.com/obra/superpowers) with flexible User Entrypoint support and phase-based verification tracking.

> For the original superpowers documentation, see [obra/superpowers](https://github.com/obra/superpowers).

## What Makes This Fork Different

### 1. Focuses in repeated testing from a User Entrypoint Perspective (UEP)

Supports 6 User Entrypoint (UEP) types:
- **Web Application** - Playwright browser testing
- **CLI Tool** - Command execution + output verification
- **API Service** - Endpoint testing via curl/test client
- **Cloud Dashboard** - Terraform Cloud, Vercel, AWS console verification
- **Library/SDK** - Unit tests + example consumer code
- **Other** - Custom verification approach

### 2. UEP Discovery & Persistence

The brainstorming skill:
1. Checks your project's `CLAUDE.md` for an existing UEP definition
2. Infers type from codebase if missing (package.json → Web, main.go → CLI, .tf files → Cloud)
3. Offers to persist the UEP so future sessions remember it

Store in your CLAUDE.md:
```markdown
## User Entrypoint
Type: CLI Tool
Verification: Command execution with output checking
```

### 3. Phase-Based Verification Tracking

Execution skills create `docs/plans/YYYY-MM-DD-<feature>-test-verification.md` at phase completion:

```markdown
## Phase 1: User can run `mytool --help`
**Verification performed:** 2026-02-09
**What was tested:**
- Ran `mytool --help`
- Confirmed output shows available commands
- Verified exit code 0
**Result:** PASS
```

This creates an audit trail of what was actually tested, not just what was planned.

### 4. Type-Specific Verification Rules

Each UEP type has explicit allowed/forbidden methods:

| UEP Type | Allowed | Not Allowed |
|----------|---------|-------------|
| Web Application | Playwright assertions, screenshots | curl, direct API calls |
| CLI Tool | Command output, exit codes | Unit tests alone |
| API Service | curl/httpie with response capture | "I checked the code" |
| Cloud Dashboard | Console state, `terraform show` | "I applied it" without checking |
| Library/SDK | Test output, example script runs | "Tests pass" without output |

## Installation

### Claude Code (via Plugin Marketplace)

Register the marketplace and install:

```bash
/plugin marketplace add obra/superpowers-marketplace
/plugin install blooperpowers@superpowers-marketplace
```

### Manual Installation

Clone this repo and point your agent to it, or copy the skills directory to your project.

## The Workflow

Same as superpowers, with UEP-aware verification:

1. **brainstorming** - Discovers your UEP, refines design, offers to persist UEP to CLAUDE.md
2. **writing-plans** - Creates UEP-appropriate phase structure with type-specific verification steps
3. **executing-plans** / **subagent-driven-development** - Executes tasks, creates verification file at each phase completion
4. **finishing-a-development-branch** - Merge, PR, keep, or discard

## Philosophy

Same as superpowers:
- Test-Driven Development
- Systematic over ad-hoc
- Complexity reduction
- Evidence over claims

Plus:
- **UEP flexibility** - Your project type shouldn't limit your workflow
- **Verification audit trail** - Know what was actually tested, not just planned

## License

MIT License - see LICENSE file

## Credits

Forked from [obra/superpowers](https://github.com/obra/superpowers) by Jesse Vincent.
