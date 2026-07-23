# golangci-lint-config

[![validate](https://github.com/TylerDavidBailey/golangci-lint-config/actions/workflows/validate.yml/badge.svg)](https://github.com/TylerDavidBailey/golangci-lint-config/actions/workflows/validate.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

An opinionated [golangci-lint](https://golangci-lint.run/) configuration for **idiomatic Go**. One file — copy it into your repo and go.

## Why

AI assistants (and humans) write Go that compiles but drifts from idiomatic style fast: ignored errors, unclosed HTTP bodies, pre-generics idioms copied from old training data. A good linter config catches all of that automatically — and auto-fixes much of it — so code review can focus on design instead of nitpicks.

This config is built to be shared: across a company, across personal projects, and publicly. Nothing is enabled implicitly (`default: none`), and every linter and non-default setting is commented in the file itself, so anyone — intern to principal — can read the config and understand what each rule does.

## Quick start

1. Copy the config into your repo root:

   ```sh
   curl -fsSLo .golangci.yml https://raw.githubusercontent.com/TylerDavidBailey/golangci-lint-config/main/.golangci.yml
   ```

2. [Install golangci-lint](https://golangci-lint.run/docs/welcome/install/) **v2.x** (this config uses the v2 schema; tested with v2.12.2).

3. Run it:

   ```sh
   golangci-lint run          # lint
   golangci-lint run --fix    # lint and apply auto-fixes
   golangci-lint fmt          # format
   ```

That's it. golangci-lint finds `.golangci.yml` in the repo root automatically, and import grouping detects your module path from `go.mod` — there is nothing to edit after copying.

## Philosophy

**A linter earns its place by catching real bugs or enforcing documented Go idiom** — Effective Go, [Go Code Review Comments](https://go.dev/wiki/CodeReviewComments), godoc conventions. Style opinions with no idiom behind them stay out, no matter how popular.

What that means in practice:

- **In:** everything that flags a real defect (`bodyclose`, `nilerr`, `makezero`, …), everything that enforces a documented convention (`revive`, `errorlint`, `errname`, staticcheck's full `ST` doc/naming checks, …), and the official `modernize` suite that moves code to current-Go idiom.
- **Out, deliberately:** linters that *fight* Go idiom or enforce taste. `noinlineerr` bans `if err := f(); err != nil` — the idiom itself. `exhaustruct` fights zero values. `varnamelen` fights short names in small scopes. `wsl`, `nlreturn`, `funlen`, `dupl`, `mnd`, `err113`, `ireturn`, and the raw complexity counters (`cyclop`, `gocyclo` — `gocognit` covers it with one generous threshold) are out for the same reason.
- **Tests get slack only where it's earned:** `gosec`, `goconst`, and `gochecknoglobals` are excluded for `_test.go` files; everything else applies to tests at full strength.

## What's inside

The [config](.golangci.yml) enables 75 linters, grouped and commented in the file:

| Group | Examples |
| --- | --- |
| Bug catchers | `bodyclose`, `nilerr`, `makezero`, `durationcheck`, `rowserrcheck` (incl. sqlx) |
| Idiom & convention | `revive`, `gocritic`, `modernize`, `errorlint`, `intrange`, `wrapcheck` |
| Logging | `zerologlint`, `sloglint`, `loggercheck` (inert unless you use those libraries) |
| Tests | `testifylint`, `thelper`, `tparallel`, `usetesting` |
| Comments, docs & hygiene | `godot`, `misspell`, `nolintlint`, `gomoddirectives` |
| Security | `gosec` |

Settings worth knowing about (each commented inline):

- **`govet` runs all analyzers** — including `nilness`, `unusedwrite`, and `waitgroup`, which the default set skips — except `fieldalignment` (memory layout over readable field order) and `shadow` (noisy).
- **`staticcheck` runs all checks** — the default silently skips the `ST` checks for package comments, naming (`ID`, not `Id`), and godoc conventions.
- **`gocritic`** runs the diagnostic, style, and performance tags with perf-paranoia, taste-only, and duplicate checks disabled.
- **`errcheck` also flags `_ = f()`** — silently discarding an error takes an explained `//nolint`, not a blank assignment.
- **Formatters:** `gofumpt` (strict superset of gofmt, extra rules on), `gci` (deterministic imports: stdlib → third-party → your module), `golines` (wraps at 120), `goimports`, `swaggo`.

## Adopting on an existing codebase

Ways to make adoption incremental when there are existing findings:

- **Auto-fix the easy stuff first.** Many linters ship fixes:

  ```sh
  golangci-lint fmt
  golangci-lint run --fix
  ```

- **Gate only new code.** Report issues only in lines changed since a revision — old code stays untouched while new code is held to the bar:

  ```sh
  golangci-lint run --new-from-rev=main
  ```

## CI

GitHub Actions, using the [official action](https://github.com/golangci/golangci-lint-action):

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v5
        with:
          go-version: stable
      - uses: golangci/golangci-lint-action@v8
        with:
          version: v2.12.2 # pin for reproducible CI
```

The action picks up `.golangci.yml` from your repo root automatically. Any other CI system just needs `golangci-lint run` on the PATH.

## Suppressing a finding (`//nolint` etiquette)

Sometimes the linter is wrong, or the code is intentionally unusual. This config requires every suppression to name the linter **and** explain itself (enforced by `nolintlint`):

```go
//nolint:gosec // G404: math/rand is fine for jitter; this is not key material
delay := time.Duration(rand.IntN(100)) * time.Millisecond
```

This keeps suppressions rare, reviewable, and removable.

## References

- [golangci-lint documentation](https://golangci-lint.run/docs/)
- [Index of all linters](https://golangci-lint.run/docs/linters/) — what each does, defaults, autofix support
- [Full reference config](https://raw.githubusercontent.com/golangci/golangci-lint/refs/heads/main/.golangci.reference.yml) — every available option, annotated
- [Effective Go](https://go.dev/doc/effective_go) and [Go Code Review Comments](https://go.dev/wiki/CodeReviewComments) — the idioms this config enforces

## License

[MIT](LICENSE) — use this config anywhere, for anything.
