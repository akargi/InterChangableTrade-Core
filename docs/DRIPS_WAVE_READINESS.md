# Drips Wave Readiness (InterChangableTrade-Core)

**Status:** readiness assessment / application preparation — **not** an application submission and **not** a guarantee of acceptance.  
**Checked:** 2026-11-09 (official Drips sources linked below).

## Project (plain language)

InterChangableTrade-Core is the public, Apache-2.0 TypeScript/NestJS backend for the InterChangableTrade ecosystem on Stellar. It exposes REST APIs (Swagger/OpenAPI at `/api/docs` when running), manages PostgreSQL-backed business logic, indexes blockchain data, and integrates with Stellar Horizon and Soroban (contract invocation, event indexing). Related projects: [InterChangabletrade-fricks](https://github.com/Chulilee/InterChangabletrade-fricks) (frontend) and [InterChangableTrade-Protocol](https://github.com/Chulilee/InterChangableTrade-Protocol) (Soroban contracts).

**Relevance to the Stellar Wave Program:** the repository is a Stellar/Soroban backend (testnet-oriented) with concrete, reviewable issues across API, indexing, trading, notifications, reliability, and developer experience. The [Stellar Wave Program](https://www.drips.network/wave/stellar) is an active Drips Wave program focused on growing the Stellar open-source ecosystem (monthly ~7-day contribution cycles; Waves 1–10 shown as completed and Wave 11 shown as upcoming on the program page as of 2026-11-09).

## Truthful maturity statement (important)

The project is in **active development** and defaults to Stellar **testnet** (`STELLAR_NETWORK=testnet`, testnet Horizon/Soroban RPC; see README/`.env.example`). Several areas are explicitly incomplete or simulated — contributors and reviewers should not treat them as production-ready:

- **Escrow:** release handling updates database records and records a placeholder settlement hash; it does not submit an on-chain funds transfer.
- **Compliance / KYC / AML:** document storage and encryption are currently placeholders, not a production encrypted-storage integration.
- **Portfolio:** USD valuations use a fixed testnet XLM estimate and zero for other assets (no live price feed).
- **Liquidity aggregation:** route execution results are mathematical simulations, not on-chain-validated.
- **Transaction coordination:** each leg invokes a contract separately; rollback changes local status without submitting an inverse transaction, so cross-leg on-chain atomicity is not guaranteed.

Security: follow [SECURITY.md](../SECURITY.md). **Never** commit secrets, real credentials, private user data, or mainnet keys. Do not make mainnet deployments/transactions. Do not disclose security-sensitive vulnerability details in public issues/docs.

## Verified Drips Wave rules (official sources)

> **Maintainer confirmation required in the Drips app:** this document summarizes official docs; it cannot confirm live in-app eligibility/approval status for this specific repository. Per-cycle repo-application limits and organizer approval are enforced in the app.

| Topic | Verified summary (see sources) | Checked |
|---|---|---|
| Program fit / status | Stellar is an active Wave Program on Drips (“Growing the Stellar Open-Source Ecosystem”, recurring Waves, reward budget shown per Wave). The program page showed Wave 11 upcoming (Nov 18–25) on 2026-11-09. | 2026-11-09 |
| Who can participate | Ecosystems run Wave Programs; maintainers apply/approve repos and scope issues; contributors apply to issues, submit PRs, and earn Points. | 2026-11-09 |
| Maintainer onboarding | Maintainer signs in with GitHub, installs the **Drips Wave GitHub App** on the org, syncs public repos, and **explicitly applies repos to a specific Wave Program**. Applications require **organizer approval** (email/in-app notification; status visible in Maintainers → Orgs and Repos). Rejected repos can be appealed in-app (cooldowns/max appeals apply); cannot be re-applied. | 2026-11-09 |
| Repo application limits | Programs may set per-user and/or per-org repo application limits per Wave cycle; limits reset each cycle; rejected applications still count for the cycle. | 2026-11-09 |
| Issue onboarding/labels | Approved repos’ issues appear in Maintainers → Issues; maintainers add issues to the Program and set complexity. A Wave Program label (e.g., `Stellar Wave`) is applied by the bot when added; maintainers may also apply the label on GitHub **only if the repo is already approved** for that Program. Issues added via GitHub label default to Trivial (100 pts) unless complexity is edited in-app. | 2026-11-09 |
| Work that qualifies / rewards | Contributors apply, get assigned, and submit PRs. **Issues must be marked resolved before the Wave ends** to earn Points for that cycle; unresolved issues award no points and can roll over. Complexity → Points: Trivial 100, Medium 150, High 200. Payouts are a share of the Wave reward pool based on points (after compliment window/processing); KYC required to apply/withdraw. Terms/rules and organizer discretion apply. | 2026-11-09 |
| Issue quality guidance | Official maintainer guidance emphasizes clear context, appropriate scope (completable within a Wave), explicit acceptance criteria/complexity, and honest point tagging (“Creating Meaningful Issues”). | 2026-11-09 |

**Official sources (checked 2026-11-09):**
- Drips Wave overview: https://docs.drips.network/wave
- Maintainers — participating / issues / approval / timing: https://docs.drips.network/wave/maintainers/participating-in-a-wave
- Maintainers FAQ: https://docs.drips.network/wave/maintainers/faq
- Repo application limits: https://docs.drips.network/wave/maintainers/repo-application-limits
- Points & rewards / timing: https://docs.drips.network/wave/points-and-rewards
- Contributors (applications, KYC, PR before deadline): https://docs.drips.network/wave/contributors/solving-issues-and-earning-rewards , https://docs.drips.network/wave/contributors/faq
- Terms & rules (eligibility/rewards at Association discretion): https://docs.drips.network/wave/terms-and-rules
- Stellar Wave Program page: https://www.drips.network/wave/stellar
- Issue quality guide: https://www.drips.network/blog/posts/creating-meaningful-issues
- Drips Wave GitHub App (repo sync/apply): https://github.com/apps/drips-wave

**Not fully confirmable from repo/public pages alone:** whether the maintainers can/should apply this specific repo right now (app login + organizer criteria), current per-cycle limit values, and maintainer review/merge availability during a given Wave. These require **maintainer confirmation**.

## Contributor setup & verified checks (run 2026-11-09)

Environment used for verification: Windows, Node `v22.18.0`, npm with lockfile; dependencies installed in a local clone via `npm ci` (creates gitignored `node_modules`). The project declares Node 20 in CI (`actions/setup-node` node-version `20`); CI is the authoritative Linux/Node-20 check.

| Command | Prerequisites | Result (2026-11-09, local clone @ `48f3512` context + PR #291 branch fixes noted) |
|---|---|---|
| `npm ci --no-audit --no-fund` | Node/npm, network/registry access | **Pass** (960 packages added; deprecation warnings only). |
| `npx tsc --noEmit` | deps installed | **Pass**. |
| `npm run build` | deps installed | **Pass** (Nest build). |
| `npx eslint "{src,libs,test}/**/*.ts" --max-warnings=0` | deps installed | **Fail (pre-existing repo-wide)** — 809 problems reported across the repo; 685 auto-fixable (mostly prettier). Files touched by PR #291 were brought to lint-clean in that branch; the baseline repo still has widespread formatting/lint debt. **Do not mass auto-fix as part of Wave onboarding without maintainer agreement** (broad churn). |
| `npx jest <touched spec paths> --runInBand --ci` | deps installed | **Pass** — 6 suites, 126 tests (escrow service/state-machine/DTO, compliance DTO, liquidity arbitrage DTO, pool analytics) after fixing 3 escrow-spec regressions found during this audit (resolved on PR #291 branch). |
| `npm test -- --testPathIgnorePatterns="\.integration\.spec\.ts$" --ci --runInBand` (hermetic) | deps installed; no PG/Redis | **Partially verified / environment-sensitive** — an earlier full run showed 70 suites passed / 7 failed (836 tests: 803 passed, 33 failed) but the ignore pattern was mis-quoted in PowerShell and the run included integration-path issues/worker exceptions; treat as **needs clean re-run in CI-like shell**. Hermetic subset for touched areas passed. |
| `npm test` (default) | **Requires PostgreSQL + Redis + app config** (and boots full app via `auth.integration.spec.ts`) | **Not runnable as “fresh clone, no services”** — matches known issue [#145](https://github.com/Chulilee/InterChangableTrade-Core/issues/145). |
| `npm run test:e2e` / integration job | PostgreSQL + Redis (+ env as in `ci.yml`) | **Not run locally** (services not provisioned). `ci.yml` defines service containers and env; it is `workflow_dispatch`-only. |
| `npm run format` | deps installed | **Mutating** (Prettier write). Not run as a gate; use check-only lint in CI-style verification. |

**Active CI (on `main`/PRs):** `.github/workflows/build-check.yml` runs `npm ci`, `npx tsc --noEmit`, `npm run build`. **Not enforced in that workflow:** lint, unit tests. `ci.yml` (lint + hermetic unit tests + integration/e2e with PG/Redis) exists but is **manual dispatch only** — enabling it on PRs is a maintainer decision (risk: pre-existing lint/test failures must be addressed or scoped).

## Application & contributor readiness assessment

**Supports readiness**
- Public Apache-2.0 repo, clear domain (Stellar/Soroban backend), active recent merges, Swagger/OpenAPI, `.env.example` with testnet defaults, Docker Compose for Postgres/Redis, issue/PR templates, SECURITY policy (private reporting), detailed README/CONTRIBUTING.
- Reproducible install (`npm ci` + lockfile) works; `tsc` and `build` pass.
- Existing backlog contains bounded issues with labels/difficulty in some cases; maintainers are actively reviewing/merging PRs (evidence: recent merges/PR activity).

**Application blockers / likely reviewer concerns**
- **Maintainer action required (cannot be done by repo alone):** install Drips Wave GitHub App, sync/apply `Chulilee/InterChangableTrade-Core` (or owning org/repo) to the Stellar Wave Program in the Drips app, pass organizer approval, and confirm per-cycle repo-application limits.
- **Maintainer capacity is unverified from repo metrics alone.** Readiness to run a Wave requires timely assignment, PR review, and marking issues **resolved before Wave end** (per official rules). Evidence of recent merges exists, but availability/commitment during a specific Wave window is a **maintainer decision**.
- **Onboarding/CI credibility gap:** default `npm test` fails on a fresh clone without PG/Redis ([#145](https://github.com/Chulilee/InterChangableTrade-Core/issues/145)); repo-wide eslint `--max-warnings=0` is currently far from green; `build-check` does not run lint/tests. Contributors may hit confusing failures.
- **Maturity/claim clarity:** README is generally careful, but Wave-facing materials must keep repeating that escrow/price/liquidity/tx-coordination/KYC storage behaviors noted above are incomplete or simulated and testnet-scoped.

## Proposed initial Wave-ready issue slate (small, from existing backlog)

> Estimates are **estimates**. “Ready to publish for the Wave” assumes the repo is **approved** in the Drips app, the issue is added with correct complexity, and a maintainer is available to assign/review/merge within the Wave window. Do not add labels or change issue text without maintainer agreement; propose exact edits if needed.

| Issue | Fit / value | Est. complexity | Key deps / skills | Publish status |
|---|---|---:|---|---|
| [#145 [Test] Stop `npm test` from running `auth.integration.spec.ts`…](https://github.com/Chulilee/InterChangableTrade-Core/issues/145) | **High onboarding value.** Directly fixes the contributor checklist mismatch; reduces Wave friction. Relevant to DX/tooling. | Trivial–Medium (estimate) | Jest config/npm scripts, docs; PG/Redis only for `test:integration`. | **Ready after maintainer confirms desired script names/split.** Clear acceptance criteria in issue. |
| [#185 [Feature]: Add an unread count and bulk mark-as-read endpoint…](https://github.com/Chulilee/InterChangableTrade-Core/issues/185) | Clear API/UX value; bounded; touches notifications module with existing patterns/e2e context. | Medium (estimate) | NestJS/TypeORM, notifications model, index/migration awareness, tests; local DB/Redis for full integration confidence. | **Needs maintainer clarification** on IDOR/path-shape migration from existing `/read/:userId` and read-state semantics (`readAt` vs status) before publishing. |
| [#186 [Feature]: Add an order book depth endpoint…](https://github.com/Chulilee/InterChangableTrade-Core/issues/186) | Strong trading/Stellar-ecosystem relevance; data exists in-memory; bounded response contract. | Medium (estimate) | Trading engine module, DTO/Swagger, aggregation semantics, per-process consistency caveat. | **Needs maintainer clarification** on `(assetCode, issuer)` lookup contract and consistency/replica wording. |
| [#195 [Performance]: Replace `redis.keys()` with `SCAN`…](https://github.com/Chulilee/InterChangableTrade-Core/issues/195) | Bounded performance/reliability fix in notifications admin/metrics paths; concrete code pointers. | Medium (estimate) | Redis/ioredis patterns (SCAN/MGET/pipeline), atomicity/TTL, tests; Redis for integration confidence. | **Needs maintainer scoping decision** — issue bundles KEYS→SCAN with atomicity/TTL/extra refactors; recommend splitting into a Wave-sized “SCAN + TTL correctness” core vs follow-ups. |

**Contributor-facing problem statements / acceptance (summary; see linked issues for full detail):**
- **#145:** Make default `npm test` run hermetic unit tests on a fresh clone (no PG/Redis); keep explicit `test:integration`/`test:e2e`; update CONTRIBUTING/CI scripts accordingly. *Tests:* default suite passes without services; integration/e2e discovery unchanged.
- **#185:** Add `/me`-scoped unread count + bulk/single-page mark-read endpoints with bounded input, single conditional updates, indexed count path, and documented semantics. *Tests:* unit + notifications e2e coverage per issue.
- **#186:** Expose aggregated, depth-bounded order book levels (no raw other-user IDs) with issuer-aware lookup and staleness/consistency metadata. *Tests:* empty/one-sided/aggregation/depth-cap cases.
- **#195 (scoped):** Remove blocking `KEYS` from notification dedup/throttle; use cursor `SCAN` (and batched reads as agreed); ensure throttle/dedupe key TTL/atomicity as scoped. *Tests:* assert no `KEYS` issued for affected paths.

**Not recommended for an initial Wave slate without splitting/design:** #192/#193/#191/#190 (large aggregation/SQL/EXPLAIN work), #194/#198/#200 (cross-cutting API contract/OpenAPI changes), #189 (schema/migration/backfill + matching semantics), security/IDOR-sensitive items requiring coordinated handling.

## Maintainer expectations (require maintainer decisions)

These are **not commitments**:
- Install/configure Drips Wave GitHub App and obtain **organizer approval** for the repo in the Stellar program.
- Confirm **review/merge capacity** for the specific Wave window and mark qualifying issues **resolved before Wave end**.
- Triage/assign applicants promptly (official guidance stresses speed during the sprint).
- Decide whether to split/clarify #185/#186/#195, and whether to prioritize #145.
- Decide CI hardening scope (enable `ci.yml` triggers? fix repo-wide lint? keep build-only gate?).

## Remaining blockers & pre-application checklist

- [ ] **Maintainer:** verify current Stellar Wave cycle/app status and repo-application limits in the Drips app; apply repo and obtain approval.
- [ ] **Maintainer:** confirm capacity for assignment, review, merge, and timely “resolved” status during the Wave.
- [ ] **Maintainer:** select/scope 2–4 issues, set complexity/acceptance criteria in GitHub + Drips, ensure no secrets/mainnet/security-sensitive disclosure.
- [ ] Address [#145](https://github.com/Chulilee/InterChangableTrade-Core/issues/145) (or clearly document the hermetic test command) so CONTRIBUTING’s checklist is trustworthy.
- [ ] Agree on CI signal (repo-wide lint status and whether/when to enable `ci.yml` on PRs).
- [ ] Ensure README/readiness language keeps **testnet + placeholder/simulation** limitations explicit.
- [ ] Confirm `SECURITY.md` process is followed for any Wave issue that touches sensitive areas.

## Application-preparation summary (for maintainers to adapt)

- **Project:** InterChangableTrade-Core — Apache-2.0 NestJS/TypeScript backend connecting InterChangableTrade to Stellar testnet (Horizon + Soroban), with REST/Swagger APIs, PostgreSQL/Redis-backed services, indexing, auth/wallet/trading/escrow/compliance/notifications modules. *Active development; some workflows simulated/placeholder — see maturity statement above.*
- **Fit for Stellar Wave (if accepted by organizers):** Stellar-native backend work with reviewable, scoped issues that support the ecosystem’s apps/contracts; aligns with “Fix, Merge, Earn” for bounded maintenance/features. *Acceptance/eligibility decided by organizers.*
- **Proposed initial slate (estimates, needs maintainer scoping):** #145 (DX/test split), #185 (notifications unread/bulk read), #186 (order book depth), #195 (Redis KEYS→SCAN/TTL/atomicity scoped) — see table/links.
- **Evidence:** `npm ci`, `tsc --noEmit`, `npm run build` passed locally (2026-11-09); targeted specs for touched modules passed (126 tests). Active CI runs build+tsc on PRs; full lint/hermetic/integration CI defined in `ci.yml` (manual) — gaps documented.
- **Limitations/mitigations:** testnet/simulated areas disclosed; no secrets/mainnet; keep sensitive vulns private per SECURITY.md; split/clarify cross-cutting issues before publishing to Wave.
- **Maintainer input needed:** app onboarding/approval, per-cycle limits, Wave-window capacity, final issue selection/complexity, and CI policy decisions. *No maintainer availability is claimed.*
