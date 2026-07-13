# Contributing

Thanks for considering a contribution. This repo is maintained by one person
in their spare time, so please bear with slow responses — there's no SLA on
issues or PRs. Small, focused PRs are far more likely to get reviewed and
merged than large ones.

## Before you open a PR

For anything beyond a typo or a small clarifying comment, **open an issue
first** to discuss the change before doing the work. This avoids spending
effort on a PR that doesn't fit the project's philosophy (see below).

## Proposing a linter change

A linter earns its place in this config by catching real bugs or enforcing
documented Go idiom (Effective Go, Go Code Review Comments, godoc
conventions). Style-enforcers with no idiom behind them — `exhaustruct`,
`varnamelen`, `wsl_v5`, `nlreturn`, `noinlineerr`, `nonamedreturns`,
`ireturn`, `err113`, `depguard` — are deliberately excluded, and proposals to
add them will be declined.

A good proposal for adding, removing, or reconfiguring a linter includes:

- **What it catches** — a real bug class, or a violation of a widely agreed
  Go idiom (not a personal style preference).
- **False-positive risk** — why this linter won't flood real codebases with
  noise.
- **Idiom citation** — for convention-enforcers, where the convention is
  documented (Effective Go, Go Code Review Comments, stdlib practice).

## Config invariants

`.golangci.yml` is the single source of truth, and it must stay
copy-and-go:

- **Self-contained:** the file must be fully understandable on its own.
  Every enabled linter carries a short inline comment saying what it flags;
  non-default settings are commented with why.
- **Explicit:** `default: none` stays. Every linter is listed by name,
  alphabetically within its group.
- **Zero setup:** nothing in the file may require editing after copying
  (no module-path placeholders, no repo-specific paths).

## Validating locally

CI runs `golangci-lint config verify` on every push and PR. Run the same
check locally:

```sh
golangci-lint config verify --config .golangci.yml
```

This config uses the golangci-lint v2 schema — make sure your local
`golangci-lint` is v2.x.

## Commit messages and PRs

Commit messages follow [Conventional Commits](https://www.conventionalcommits.org/)
(`feat`, `fix`, `docs`, `chore`, etc.). Keep PRs focused on one change so
they're easy to review.

## License

By contributing, you agree that your contributions are licensed under this
repo's [MIT License](LICENSE).
