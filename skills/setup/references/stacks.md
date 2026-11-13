# Default verbs per ecosystem
Use only when the repo has no runner or CI step for the verb. Detect by the marker file; prefer the lockfile's package manager.

| Ecosystem | Marker | test (targeted) | lint | typecheck | gen hints |
|---|---|---|---|---|---|
| Go | `go.mod` | `go test -race ./{pkg}/...` | `golangci-lint run` (if configured) or `go vet ./...` | `go build ./... && go vet ./...` | `go generate`, sqlc, oapi-codegen, `*.gen.go` |
| TS/JS | `package.json` + `pnpm-lock`/`package-lock`/`yarn.lock`/`bun.lock` | `<pm> vitest related --run {files}` or `<pm> jest --findRelatedTests {files}` | `<pm> eslint {files}` or biome | `<pm> tsc --noEmit` | openapi-typescript, graphql-codegen, prisma |
| Python | `pyproject.toml` (+ `uv.lock`/`poetry.lock`) | `uv run pytest {files}` (or `poetry run`) | `uv run ruff check {files}` | `uv run mypy` or pyright, if configured | datamodel-codegen, protoc |
| Rust | `Cargo.toml` | `cargo test -p {pkg}` | `cargo clippy -- -D warnings` | `cargo check` | build.rs, prost |
| Java/Kotlin | `pom.xml` / `build.gradle(.kts)` | `./mvnw -pl {pkg} test` / `./gradlew :{pkg}:test` | spotless/ktlint/checkstyle task | compile task | openapi-generator, protobuf plugin |
| C#/.NET | `*.sln` / `*.csproj` | `dotnet test {pkg} --filter …` | `dotnet format --verify-no-changes` | `dotnet build` | NSwag |
| Ruby | `Gemfile` | `bundle exec rspec {files}` | `bundle exec rubocop {files}` | sorbet/steep if present | – |
| PHP | `composer.json` | `vendor/bin/phpunit {files}` | phpcs / php-cs-fixer | phpstan / psalm | – |
| Elixir | `mix.exs` | `mix test {files}` | `mix credo` + `mix format --check-formatted` | `mix compile --warnings-as-errors` | – |
| Swift | `Package.swift` / `*.xcodeproj` | `swift test --filter {pkg}` / xcodebuild test | swiftlint | `swift build` | – |
| Dart/Flutter | `pubspec.yaml` | `flutter test {files}` | `dart analyze` | (analyze) | build_runner |

Runners that wrap the above (use their targets when present): `Makefile`, `Taskfile.yml`/`./task`, `justfile`, npm scripts, nx/turbo
(`affected`), Bazel/Pants (`test //…`), `mise`/`asdf` tasks. CI workflow steps are the source of truth for which verbs exist.
