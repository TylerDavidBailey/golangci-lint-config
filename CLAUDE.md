# CLAUDE.md

Public repo shipping one opinionated golangci-lint config for idiomatic Go.
Users copy `.golangci.yml` into their repo root and go — it is the single
source of truth and the only deliverable here.

## Rules

- A linter earns its place by catching real bugs or enforcing documented Go
  idiom (Effective Go, Go Code Review Comments, godoc conventions). Style
  opinions with no idiom behind them stay out.
- The config must stay copy-and-go: self-contained, `default: none`, every
  linter listed alphabetically within its comment group with a short inline
  hint, non-default settings commented with why. Nothing may require editing
  after copying (no module-path placeholders).
- Uses the golangci-lint v2 schema (tested with v2.12.2).

## Validating changes

```sh
golangci-lint config verify --config .golangci.yml
```

Schema verification misses runtime errors (bad rule names, analyzer names),
so for non-trivial changes also run `golangci-lint run` against a scratch Go
module using this config.
