# Allyourbase — Roadmap

**Last updated:** 2026-03-27
**Status:** Core backend, dashboard, and developer tooling are shipped. All 72 audit-ledger rows (R01-R72) are green — no remaining yellow/red proof gaps. Recent mar26/mar27 work fixed four CI integration blockers: pgmanager SHA256SUMS path matching, sbmigrate uuid-ossp extension assumption, storage quota concurrent upload timeout, and the broad fast-suite flake in `make test-all`. Managed Postgres pipeline now includes a Zonky fallback provider. Staging CI sync completed and verified. See `_dev/FEATURES.md` for canonical feature inventory, `_dev/AUDIT_LEDGER.md` for row-level proof status, and `_dev/PHASES.md` for execution details.

## Feature Status

| Feature | Status | Notes |
|---------|--------|-------|
| Single-binary PostgreSQL backend | ✅ | Core runtime and managed local Postgres flow are shipped. |
| Auto-generated REST API, auth, realtime, and RLS | ✅ | Core data and auth surface is shipped. |
| Storage, admin dashboard, and schema tooling | ✅ | Main operational workflows are available. |
| MCP server for coding tools | ✅ | `ayb mcp` tool/resource/prompt surface is available. |
| Web hosting MVP (`ayb sites deploy`) | ✅ | Deploy/promote/rollback static-site workflow is available. |
| CLI UX overhaul and guided output | ✅ | Shared CLI UX layer is shipped. |
| `ayb init` scaffolding and demo apps | ✅ | Project bootstrap and demos are available. |
| Backup/restore plus operational CLI commands | ✅ | `db backup/restore`, `stats`, `rpc`, and `query` are available. |
| Documentation accuracy and multi-SDK code examples | ✅ | MAR22-PM-01 verified and corrected the remaining 65 flagged docs code examples across published guides. |
| CI coverage for SDK integration and CLI E2E | ✅ | MAR22-PM-02 added dedicated CI jobs for the SDK real-server suite and CLI E2E coverage. |
| Migration tooling across supported sources | 🟡 | PocketBase and Supabase active-scope paths now have live scripted evidence; Firebase remains maintenance-only and is not yet live-validated on real exports. |
| Fuzz-hardening for auth/JWT/deserialization paths | 🟡 | JWT, API filter, and realtime filter fuzz targets exist. RPC deserialization and MIME validation still pending. |

## Priorities

See [PRIORITIES.md](PRIORITIES.md) for the current priority stack.

## Recent Completed Work

- `MAR27-PM-3` refreshed Supabase live-validation evidence on the local CLI profile, confirmed the recent self-hosted refresh, and fixed the live-fixture seed harness so custom `auth.users` triggers that write to `public.profiles` no longer break the scripted proof path. Public/internal status docs now reflect that PocketBase + Supabase are the validated active scope, while Firebase remains deferred.
- `MAR27-PM-3` also hardened installer auto-detection so the public install script selects the latest `v*` AYB app release instead of mistakenly following the newer auxiliary `pg-*` release stream.
- `MAR27-PM-2` restored the broad fast-suite baseline. The fix combined an integration-only package runner for `internal/api` / `internal/server`, helper guardrails that reject integration/unit test-name collisions, targeted `internal/api` integration test renames to keep the helper truly integration-only, and a no-cache pgx integration pool in `internal/testutil/pgcontainer.go` to eliminate `cached plan must not change result type` flakes after destructive schema resets. Validation on 2026-03-27: `bash scripts/run-integration-tests.sh` passed 3 times, `make test-all` passed 3 times, and `internal/api` integration-only stress passed 10 times.
- `MAR26-PM-1` fixed pgmanager test harness SHA256SUMS path mismatch (handler matched exact `/SHA256SUMS` but `sha256SumsURL()` produces `/16/SHA256SUMS`), made integration start timeout configurable via `AYB_PGMANAGER_TEST_START_TIMEOUT`, fixed `TestPortAlreadyInUse` false positive on Linux by using a raw TCP listener instead of a second Postgres instance. Completed staging sync and CI verification.
- `MAR26-PM-2` removed the `uuid-ossp` extension dependency from the sbmigrate FK-chain skip test (`TestE2E_SchemaMigrationSkipsIncompatibleFKChain`) by replacing `uuid_generate_v4()` with `gen_random_uuid()` and a custom SQL function. Test is now environment-agnostic for public CI.
- `MAR26-PM-3` fixed the `TestStorageQuotaConcurrentUploads` timeout by adding HTTP client timeouts to the test helper `uploadFile` function and hardening the storage handler quota-check/upload path. Confirmed `internal/storage` does not need package-level serialization in the CI runner.
- `MAR26-BATCH` (pre-session staged work): added pgmanager Zonky fallback provider, integration test runner script (`scripts/run-integration-tests.sh`), email template refactoring, config types expansion, codehealth guardrails, browser test fixtures for sites deploy lifecycle, Debbie sync configuration, and CI workflow alignment with managed Postgres.
- `MAR24-PM-1` created the audit ledger pardon/burndown system (`_dev/AUDIT_LEDGER.md`), reconciled contradictions between FEATURES.md and PHASES.md (OAuth-specific 403 claim vs generic RLS reality), and established row-level evidence tracking for R01-R31.
- `MAR24-PM-2` closed auth/security proof gaps: OAuth auth restrictions spec (readonly scope 403, allowed_tables 403, auth-code RLS boundaries), JWT rotation invalidation proof, and private edge-function bearer-auth proof. Promoted R03, R07, R09 from yellow/red to green.
- `MAR24-PM-3` closed webhook/trigger proof gaps: trigger enable/disable/re-enable proof for db/cron/storage, end-to-end HMAC signature verification via local ephemeral target server, retry attempt sequence validation. Added disabled-cron 409 guard in backend. Promoted R13, R16, R17 to green. Also made RPC OpenAPI paths POST-only to match server routing.
- `MAR24-PM-4` fixed the scaffold-to-SDK contract: the JS SDK now exposes `health()`, generated scaffold templates call real SDK methods, browser scaffolds keep session tokens in memory instead of `localStorage`, and generated Node/Express starters surface clearer bootstrap guidance when the starter schema has not been loaded yet.
- `MAR24-PM-5` fixed webhook PATCH clear-events semantics, added explicit "save this password now" guidance to the startup banner, improved the table browser zero-state copy, and reduced `_dev/dx-audit-services.md` to one remaining open recommendation.
- `MAR24-PM-6` fixed several verification regressions: claimless-RLS import/export hardening, request-log IP normalization on admin reads, smoke/browser spec stabilization across edge/realtime/SAML/webhook/blog journeys, and app-scoping docs refreshes. Those fixes improved targeted package/browser runs, but the broad fast-suite baseline was not fully restored until `MAR27-PM-2`.
- `MAR23-WS-1` corrected the RPC notify documentation, expanded SDK RPC coverage for set-returning and error paths, added an RPC→Realtime notify integration suite, and marked client RPC invocation as browser-verified in `_dev/FEATURES.md`.
- `MAR23-WS-3` completed a services DX audit across webhooks, jobs, secrets, storage, and edge functions, corrected docs/tracker wording, removed the brittle external webhook dependency from browser coverage, and published `_dev/dx-audit-services.md`.
- `MAR22-PM-01` closed the long-running docs verification backlog by auditing and fixing the remaining 65 `<!-- TODO: verify -->` code examples across the published guides.
- `MAR22-PM-02` wired the SDK real-server integration suite and the CLI E2E suite into GitHub Actions so those paths now get first-class regression coverage in CI.
- `MAR22-PM-03` hardened PocketBase migration with added regression coverage around indexes, rollback behavior, file-copy failures, nil custom fields, and unsupported RLS constructs.

## Open / Not Yet Implemented

| ID | What | Status | Notes |
|----|------|--------|-------|
| **firebase-migration-live-validation** | Firebase live-export validation | ⏸️ | PocketBase + Supabase active-scope migration proof is in place; Firebase remains maintenance-only until customer demand justifies a fresh live run. |
| **fuzz-remaining** | RPC deserialization and MIME validation fuzz targets | 🟡 | JWT/API/realtime filter fuzz exists. |

## Archive

When this list grows stale or too large, move completed items to `roadmap-history/YYYY-QN.md`.
