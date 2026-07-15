# StellarVote

**Developer-first election infrastructure platform.** Add secure, transparent, and verifiable elections to any application with a few API calls.

Every vote is cryptographically committed to the Stellar blockchain, providing a tamper-evident verification layer while preserving voter privacy.

## Quick Start

```bash
git clone https://github.com/dangerouslyStellar/stellarvote.git
cd stellarvote
cp .env.example .env
docker compose up -d
go mod download
go run ./cmd/api
```

The API starts on `http://localhost:8080`.

## Documentation

- [Contributing Guide](./CONTRIBUTING.md)
- [Product Requirements](./docs/PRD.md)

## Makefile Commands

| Command | Description |
| --- | --- |
| `make all` | Build and run tests |
| `make build` | Build the application |
| `make run` | Run the application |
| `make test` | Run the test suite |
| `make watch` | Live reload the application |
| `make clean` | Clean up binary from the last build |
| `make docker-run` | Start PostgreSQL and Redis containers |
| `make docker-down` | Shut down DB containers |
| `make itest` | Run integration tests |

## Project Structure

```
├── cmd/
│   └── api/            # API server entrypoint
├── docs/               # Documentation and specs
├── go.mod
└── .github/workflows/  # CI pipelines
```

## License

MIT
