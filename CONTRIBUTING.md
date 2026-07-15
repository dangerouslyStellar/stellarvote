# Contributing to StellarVote

First off, thank you for considering contributing. StellarVote is a developer-first election infrastructure platform, and every contribution — whether a bug report, feature request, documentation improvement, or pull request — helps make digital elections more transparent and verifiable.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Workflow](#development-workflow)
- [Pull Request Process](#pull-request-process)
- [Coding Standards](#coding-standards)
- [Testing](#testing)
- [Commit Conventions](#commit-conventions)
- [Issue Tracking](#issue-tracking)
- [Security](#security)

## Code of Conduct

This project adheres to the [Contributor Covenant](./CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code. Report unacceptable behavior to the maintainers.

## Getting Started

### Prerequisites

- Go 1.24+
- Docker & Docker Compose
- Node.js 20+ (for the frontend embed SDK)
- A Stellar testnet account (for blockchain integration testing)

### Local Setup

```bash
# Clone the repository
git clone https://github.com/dangerouslyStellar/stellarvote.git
cd stellarvote

# Copy environment template
cp .env.example .env

# Start dependencies (PostgreSQL, Redis)
docker compose up -d

# Download Go dependencies
go mod download

# Run the development server
go run ./cmd/api
```

The API will start on `http://localhost:8080`. See the [README](./README.md) for detailed setup instructions.

## Development Workflow

### Branch Strategy

- `deploy` — default branch; all pull requests target this branch.
- `main` — release-only; holds production-ready code merged from `deploy`.
- Feature branches — create from `deploy` and name them descriptively:

```
feat/ranked-choice-voting
fix/voter-duplicate-validation
docs/api-authentication
```

### Branch Protection

Both `main` and `deploy` are protected. Direct pushes are blocked. All changes must go through a pull request with passing status checks.

## Pull Request Process

1. **Create a feature branch** from `deploy`.
2. **Make your changes** following the coding standards below.
3. **Run tests and lint** locally before pushing:

   ```bash
   go test -race -count=1 ./...
   golangci-lint run ./...
   ```

4. **Open a pull request** against `deploy`.
5. **Ensure all checks pass** — the CI runs lint, security scanning (govulncheck + gosec), unit tests, and a Trivy container scan. All must be green before merging.
6. **Request a review** from a maintainer.
7. **Squash-merge** after approval (the deploy branch receives squashed commits; main receives the full merge).

> [!IMPORTANT]
> Pull requests that do not target `deploy` will be redirected or closed. Only maintainers merge into `main` as part of a release.

## Coding Standards

### Go

- **Formatting**: `gofumpt` (tighter variant of `gofmt`). Run `gofumpt -w .` before committing.
- **Linting**: `golangci-lint` with the project's [.golangci.yml](./.golangci.yml) config. All lint warnings must be addressed.
- **Naming**: Follow [Effective Go](https://go.dev/doc/effective_go) conventions. Exported symbols get doc comments.
- **Error handling**: Wrap errors with context. Prefer `fmt.Errorf("…: %w", err)` over bare `err` returns.
- **Imports**: Group standard library, third-party, and internal packages (separated by a blank line).
- **Zero tolerance for `G404` (insecure random)** — use `crypto/rand`, not `math/rand`, for anything security-sensitive.

### API Design

- RESTful endpoints under `/v1/`.
- Request/response bodies use JSON with `snake_case` field names.
- Pagination uses `cursor`-based tokens.
- Errors follow [RFC 9457](https://www.rfc-editor.org/rfc/rfc9457) (Problem Details).

### SDK & Frontend

- TypeScript SDK in `sdk/` follows the patterns in existing modules.
- Embed components use the framework-agnostic web component pattern (`<stellarvote-widget>`).
- Tests use Vitest and Playwright for component tests.

## Testing

- **Unit tests**: Required for all new logic. Run with `go test -race -count=1 ./...`.
- **Integration tests**: Tagged with `//go:build integration`. Run with `go test -tags=integration ./...` (requires Docker services up).
- **Frontend tests**: `npm test` in the `sdk/` directory.
- **Coverage**: Aim for 80%+ on new code. Coverage reports are uploaded to Codecov on each PR.

## Commit Conventions

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>: <short summary>

[optional body]
```

Types:

| Type       | When to use                          |
| ---------- | ------------------------------------ |
| `feat`     | A new feature                        |
| `fix`      | A bug fix                            |
| `docs`     | Documentation changes                |
| `refactor` | Code restructuring (no behavior change) |
| `test`     | Adding or updating tests             |
| `chore`    | Build, CI, tooling, dependencies     |
| `security` | Security fixes or hardening          |

Examples:

```
feat: add ranked-choice vote tallying
fix: validate voter eligibility before ballot access
docs: document vote verification API
chore: bump golangci-lint to v2.12
```

## Issue Tracking

- **Bug reports**: Include steps to reproduce, expected vs actual behavior, environment details.
- **Feature requests**: Describe the use case, not just the solution. The "why" helps us design better.
- **Security issues**: See [Security](#security) below — do not file a public issue.

Use the provided issue templates when available.

## Security

If you discover a security vulnerability, **do not open a public issue**. Instead, email the maintainers directly or use the GitHub Security Advisory tab.

We take the following seriously:

- Cryptographic weaknesses in vote commitments.
- Authentication or authorization bypasses.
- Vote integrity or privacy violations.
- Dependency vulnerabilities (we run govulncheck and Trivy in CI).

## Questions?

Open a [discussion](https://github.com/dangerouslyStellar/stellarvote/discussions) or ask in the project's community channel.
