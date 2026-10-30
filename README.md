# go-standards

Reusable Go code standards for `cwhight` projects. Designed to be referenced from any repo's `CLAUDE.md` so AI agents (Claude Code, Cursor, etc.) automatically follow them.

## Files

`go.md` applies to every Go change. Open the others when a change touches their area.

| File | What it covers | Read it when the change |
|------|----------------|-------------------------|
| `go.md` | Layers, interfaces, errors, code style, anti-patterns, PR size | Always |
| `architecture.md` | Domains, ports and adapters, third-party services, startup config, package layout | Adds a service, a dependency or a new package |
| `observability.md` | Logging, metrics, alerting, tracing | Adds logs, counters, alerts or spans |
| `concurrency.md` | Panic recovery in goroutines, worker failure and retry semantics | Starts a goroutine or adds a worker, cron or consumer |
| `service-lifecycle.md` | Startup and shutdown ordering, readiness, drain timing | Touches `main` or shutdown behaviour |
| `database.md` | Connection protection, enum columns | Opens a pool or changes a table |
| `feature-flags.md` | Flag types, lifecycle, wiring flags behind interfaces | Puts new behaviour behind a flag |
| `testing.md` | Unit tests, mocks, database tests, E2E tests, behaviour matrices | Writes or reviews tests |

## Install in a new project

### Option 1: CLAUDE.md reference (recommended for AI agents)

Add this block to your project's `CLAUDE.md`:

```markdown
## Code Standards

This project follows the Go standards defined in [cwhight/go-standards](https://github.com/cwhight/go-standards).

Before writing or reviewing Go code, fetch the current standards. Always read go.md and testing.md,
plus any topic file whose area the change touches (see the table in the repo README):

    for f in go testing; do
      gh api repos/cwhight/go-standards/contents/$f.md --jq '.content' | base64 -d
    done

All Go code in this repo must conform to these standards.
```

This tells any AI agent exactly where to find the standards and how to fetch them. The agent reads your `CLAUDE.md` at session start and will fetch + follow the standards when writing Go code.

### Option 2: Makefile sync

Add a target to pull the latest standards into your repo:

```makefile
STANDARDS := go architecture observability concurrency service-lifecycle database feature-flags testing

.PHONY: sync-standards
sync-standards:
	@mkdir -p standards
	@for f in $(STANDARDS); do \
		gh api repos/cwhight/go-standards/contents/$$f.md --jq '.content' | base64 -d > standards/$$f.md; \
	done
	@echo "Standards synced from cwhight/go-standards"
```

Then reference the local copy in your `CLAUDE.md`:

```markdown
## Code Standards

Single source of truth: `standards/go.md` and `standards/testing.md`, plus the topic files in `standards/`
that match the area being changed. Sync with `make sync-standards`. All Go code must conform.
```

### Option 3: Git submodule

```bash
git submodule add git@github.com:cwhight/go-standards.git standards
```

Then in `CLAUDE.md`:

```markdown
## Code Standards

Single source of truth: `standards/go.md` and `standards/testing.md`, plus the topic files in `standards/`
that match the area being changed (git submodule from cwhight/go-standards). All Go code must conform.
```

## For AI agents reading this

If you've been pointed here from another repo's `CLAUDE.md`, read `go.md` and `testing.md` in full. Then open each topic file from the table above whose trigger matches the change. These are the rules you must follow when writing, reviewing, or refactoring Go code in that project. Every rule in a file you open applies.
