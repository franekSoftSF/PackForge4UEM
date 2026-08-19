# PackForge4UEM — Project Status

> **Generated from [`status.json`](status.json). Do not edit by hand.**
> Snapshot: **2026-08-19** · Deadline: **2026-09-15** · License: **MIT** · Repository: [franekSoftSF/PackForge4UEM](https://github.com/franekSoftSF/PackForge4UEM)

| | |
| --- | --- |
| Overall state | `planning` |
| Progress | **1 / 48 tasks** (2%) |
| Current milestone | **W1 — Foundation and contracts** (19–25 Aug) |
| Open questions | 3 (1 blocking) |
| High risks | 2 |

---

## Milestones

| ID | Milestone | Window | State | Goal |
| --- | --- | --- | --- | --- |
| **W1** | Foundation and contracts | 19–25 Aug | 🔵 in progress | Repo, solution, OpenAPI contract, domain model, Angular skeleton, CI, compose stack boots |
| **W2** | UEM integration and agent MVP | 26 Aug – 1 Sep | ⚪ todo | End-to-end publish of one package to one tenant; agent executes a build job |
| **W3** | Multi-tenant, versioning, App Volumes | 2–8 Sep | ⚪ todo | Fan-out to N tenants, immutable versioning, App Volumes artifacts, job queue UI |
| **W4** | Hardening and launch | 9–15 Sep | ⚪ todo | Security pass, docs, demo, publication content for Omnissa Community, blog and LinkedIn |

## Ownership

| Track | Owner | Scope |
| --- | --- | --- |
| Frontend | **Codex** | Angular 22 console, components, state, e2e tests |
| Backend | **Cloud** | API, domain, persistence, UEM and App Volumes integrations, agent, CI |

The two tracks synchronise on exactly one thing: the **OpenAPI contract**, generated from the backend and consumed by the console as a typed client. It is frozen in W1, before parallel work begins.

## MVP scope

**In:** MSI packages · Windows internal applications in UEM · Freestyle Orchestrator workflow generation · App Volumes AppStack as a package artifact · App Volumes Agent as a managed prerequisite · multi-tenant publish fan-out

**Out (v0.2):** MSIX · macOS packages · App Volumes capture VM automation · package approval workflows · RBAC beyond a single admin role

---

## Epics

| ID | Epic | Owner | Milestone | Done | State |
| --- | --- | --- | --- | --- | --- |
| E01 | Foundation and contracts | Cloud | W1 | 0/4 | ⚪ todo |
| E02 | Domain model and persistence | Cloud | W1 | 0/4 | ⚪ todo |
| E03 | Package manifest and templates | Cloud | W2 | 0/4 | ⚪ todo |
| E04 | Workspace ONE UEM integration | Cloud | W2 | 0/4 | ⚪ todo |
| E05 | App Volumes integration | Cloud | W3 | 0/3 | ⚪ todo |
| E06 | Multi-tenant publishing | Cloud | W3 | 0/4 | ⚪ todo |
| E07 | Agent wrapper | Cloud | W2 | 0/5 | ⚪ todo |
| E08 | Console UI | Codex | W2 | 0/6 | ⚪ todo |
| E09 | Security | Cloud | W4 | 0/4 | ⚪ todo |
| E10 | Packaging and deployment | Cloud | W4 | 0/4 | ⚪ todo |
| E11 | Documentation and launch | Cloud + Codex | W4 | 1/6 | 🔵 in progress |

### E01 · Foundation and contracts — Cloud, W1

- [ ] **E01-T01** Repository scaffold — solution (`.slnx`), directory layout, `.gitignore`, editorconfig (LICENSE already present)
  *DoD: solution builds clean on a fresh clone*
- [ ] **E01-T02** Define the OpenAPI contract for packages, versions, tenants, jobs and publications
  *DoD: contract reviewed with the frontend track; typed client generates without errors*
- [ ] **E01-T03** CI pipeline — build, test, container image, OpenAPI artifact publication
  *DoD: green pipeline on main; client regeneration fails the build on contract drift*
- [ ] **E01-T04** Docker Compose skeleton — API container, volumes for database and artifacts
  *DoD: `docker compose up` serves a health endpoint over TLS*

### E02 · Domain model and persistence — Cloud, W1

- [ ] **E02-T01** Domain model — Package, PackageVersion, Artifact, Tenant, Job, Publication, AuditEntry
  *DoD: versions immutable once built; App Volumes artifacts modelled from day one (D11)*
- [ ] **E02-T02** NHibernate and FluentNHibernate mappings against SQLite, WAL mode, serialised writes
  *DoD: no array `.Contains()` in queries — it fails at runtime, not compile time; every query executed against a live database, not only unit tested*
- [ ] **E02-T03** FluentMigrator baseline migration, applied on API startup
  *DoD: upgrade from an empty database and from the previous version both verified*
- [ ] **E02-T04** Artifact store — SHA-256 addressed filesystem layout, retention and orphan cleanup
  *DoD: hash mismatch rejects the artifact; store survives container recreation*

### E03 · Package manifest and templates — Cloud, W2

- [ ] **E03-T01** Manifest schema v1 plus published JSON Schema
  *DoD: an invalid manifest is rejected before a job starts, with a field-level message*
- [ ] **E03-T02** Template inheritance resolver — `msi-repack`, `exe-wrap`, `appstack-only`
  *DoD: a typical package manifest is under twenty lines; imperative steps available as an escape hatch*
- [ ] **E03-T03** Manifest import and export, from file and from the console
  *DoD: round trip preserves the manifest byte for byte; raw manifest retained for provenance*
- [ ] **E03-T04** Reproducibility — manifest plus source hash determines the artifact
  *DoD: rebuilding the same version produces an identical hash*

### E04 · Workspace ONE UEM integration — Cloud, W2

- [ ] **E04-T01** UEM client — OAuth 2.0 client credentials, retries, rate limit handling
  *DoD: token refresh transparent to callers; failures surfaced with the tenant that produced them*
- [ ] **E04-T02** Chunked blob upload and internal application creation — ⛔ blocked by **Q01**
  *DoD: a multi-hundred-megabyte MSI uploads and appears in the tenant*
- [ ] **E04-T03** Mock UEM server for development and integration tests
  *DoD: the full publish path is testable with no live tenant*
- [ ] **E04-T04** Freestyle workflow generator — manifest to workflow JSON, including the App Volumes Agent conditional first step — ⛔ blocked by **Q01**
  *DoD: generated workflow imports successfully; install order and conditions verified on a device*

### E05 · App Volumes integration — Cloud, W3

- [ ] **E05-T01** App Volumes Manager client — authentication and inventory of packages and AppStacks
  *DoD: existing AppStacks listed and mapped to console packages*
- [ ] **E05-T02** AppStack registration and update from a package version
  *DoD: every AppStack traceable back to the manifest version that produced it*
- [ ] **E05-T03** App Volumes Agent as a managed prerequisite package, with version floor enforcement
  *DoD: agent absence detected and remediated as the first workflow step; one agent version managed centrally, not duplicated per package*

### E06 · Multi-tenant publishing — Cloud, W3

- [ ] **E06-T01** Tenant registry — environment URL, credentials in a secret store, organisation group, per-tenant assignment mapping
  *DoD: secrets never returned through the API; every credential use audited*
- [ ] **E06-T02** Logical name to tenant-local ID resolver for applications and smart groups
  *DoD: an unresolvable name fails the publication with a clear message, before any write to the tenant*
- [ ] **E06-T03** Publication fan-out — one version to N tenants, per-tenant state and log
  *DoD: partial failure isolated to its tenant; a single tenant can be retried without republishing the rest*
- [ ] **E06-T04** Idempotent republish — update the existing workflow instead of cloning it
  *DoD: ten publishes leave exactly one workflow per tenant; console-managed workflows identifiable inside the tenant*

### E07 · Agent wrapper — Cloud, W2

- [ ] **E07-T01** Windows service host, installer, configuration and logging
  *DoD: installs and self-recovers after a station reboot*
- [ ] **E07-T02** mTLS enrolment and authentication, private key held in the Windows certificate store
  *DoD: revoking a certificate server-side locks the agent out immediately*
- [ ] **E07-T03** Job source behind an interface — MQTT push plus polling fallback
  *DoD: agent keeps working with the broker down; an offline agent is detected via the MQTT last will*
- [ ] **E07-T04** Build execution — download and verify source, run template steps, produce and hash artifacts
  *DoD: build output reproducible; step-level progress visible in the console*
- [ ] **E07-T05** Chunked resumable artifact upload
  *DoD: an interrupted transfer resumes rather than restarting the build*

### E08 · Console UI — Codex, W2

- [ ] **E08-T01** Angular 22 application shell, routing, typed API client generated from OpenAPI
  *DoD: client generated, not hand-written*
- [ ] **E08-T02** Package catalogue — list, detail, version history and diff between versions
  *DoD: version provenance visible, including source hash and manifest*
- [ ] **E08-T03** Manifest editor with schema validation and template selection
  *DoD: validation errors shown against fields before submission*
- [ ] **E08-T04** Job queue with live progress and log streaming
  *DoD: build progress visible without a page refresh*
- [ ] **E08-T05** Tenant registry and publication matrix — one version across N tenants
  *DoD: per-tenant state and single-tenant retry available from the UI*
- [ ] **E08-T06** Audit log view
  *DoD: who published what, where and when, filterable*

### E09 · Security — Cloud, W4

- [ ] **E09-T01** TLS termination in Kestrel with a mounted certificate, HSTS, modern cipher configuration
  *DoD: no plaintext listener exposed*
- [ ] **E09-T02** Administrator authentication for the console
  *DoD: no default credentials shipped*
- [ ] **E09-T03** Secret storage encrypted at rest, write-only through the API
  *DoD: tenant credentials unreadable from the database alone*
- [ ] **E09-T04** Security review of the agent trust boundary and the upload path
  *DoD: findings triaged before publication*

### E10 · Packaging and deployment — Cloud, W4

- [ ] **E10-T01** Production Compose stack — API, optional MQTT broker profile, volumes, healthchecks
  *DoD: clean clone to running console in a single command*
- [ ] **E10-T02** Large upload tuning — request limits and timeouts sized for multi-hundred-megabyte artifacts
  *DoD: a 1 GB artifact uploads without a limit error*
- [ ] **E10-T03** Backup and restore procedure for the database and artifact store
  *DoD: restore verified on a clean host*
- [ ] **E10-T04** Release process — versioned container image, tagged release, upgrade notes
  *DoD: upgrade from the previous release preserves packages*

### E11 · Documentation and launch — Cloud + Codex, W4

- [x] **E11-T01** README with problem statement, architecture diagram and manifest example
  *DoD: understandable to a reader who has never seen the project*
- [ ] **E11-T02** `status.json` and `status.md`, with a generator for the readable view — 🔵 in progress
  *DoD: `status.md` never edited by hand*
- [ ] **E11-T03** Installation and first-package walkthrough
  *DoD: a new user publishes a package by following the document alone*
- [ ] **E11-T04** Manifest and template authoring reference
  *DoD: community can contribute templates without reading the source*
- [ ] **E11-T05** Demo recording of the end-to-end flow
  *DoD: shows one package reaching two tenants*
- [ ] **E11-T06** Launch content — Omnissa Community post, blog article, LinkedIn announcement
  *DoD: published by 2026-09-15*

---

## Decisions

| ID | Decision | Rationale |
| --- | --- | --- |
| **D01** | NHibernate with FluentNHibernate for data access | Explicit mapping control; consistent with other projects in this estate |
| **D02** | Versioned migrations via FluentMigrator, not `SchemaUpdate` | Community users will upgrade between releases and must keep their data |
| **D03** | SQLite holds metadata only; binaries on an artifact volume, referenced by path plus SHA-256 | Multi-hundred-megabyte blobs in SQLite would wreck both the database and backups |
| **D04** | SQLite in WAL mode with writes serialised in the backend | Prevents `SQLITE_BUSY` under concurrent fan-out; must be designed in from the start |
| **D05** | Kestrel serves the Angular build and terminates TLS; no separate reverse proxy | HAProxy cannot serve static files, so it would add a layer rather than replace one. One container, one config language, lower barrier for contributors *(supersedes the earlier nginx assumption)* |
| **D06** | Docker Compose is the unit of deployment; the agent stays outside Docker as a Windows service | Backend stays portable and stateless; MSI work has to happen on Windows |
| **D07** | Agents authenticate with mTLS client certificates, key in the Windows certificate store | The agent can execute commands on the station and publish to production tenants — the most valuable component to compromise. Revocation is server-side |
| **D08** | MQTT carries notifications, progress and heartbeat/LWT; HTTPS carries job state and artifacts; polling fallback mandatory | MQTT gives NAT-friendly push and free offline detection, but is not a durable job queue and cannot move large files. The broker must not be a single point of failure |
| **D09** | A JSON package manifest is the user-facing interface to automation; templates compose by inheritance | Engineers describe intent instead of digging into the build engine; imperative steps remain as an escape hatch |
| **D10** | Freestyle Orchestrator is the deployment engine; the console generates workflow JSON and builds no competing DSL | Install order, conditions and branching already execute natively in the platform. Removes an entire orchestration engine from scope |
| **D11** | App Volumes Agent is a separate managed prerequisite enforced as the first conditional Freestyle step (variant A), not chained into every MSI (variant B) | Variant B would duplicate a platform capability and produce N copies of the agent, with version conflicts and painful upgrades |
| **D12** | Manifests reference logical names; a resolver maps them to tenant-local IDs at publish time, and republishing is idempotent | Freestyle identifiers are tenant-local, so one workflow file cannot be copied across tenants. This is the core value of the product |
| **D13** | Codex owns frontend; Cloud owns backend, agent, integrations and CI; OpenAPI is the contract | Enables parallel work with a single synchronisation point |
| **D14** | MVP is narrow: MSI plus Windows internal apps plus multi-tenant publish | Four weeks to deadline; scope discipline is the only way the date holds |
| **D15** | `status.json` is the source of truth, `status.md` is generated from it | Machine-readable state for tooling, readable view for humans, no divergence |
| **D16** | MIT license | Chosen by the project owner; shortest and most permissive, maximises adoption |
| **D17** | Repository is `franekSoftSF/PackForge4UEM` | Created by the project owner |
| **D18** | Renamed to **PackForge4UEM** before any code was written | Drops "Manager", which collided with App Volumes Manager (an actual Omnissa server component), and removes the leading hyphen that CLI tools parse as an option flag. Free now, expensive after publication |

## Open questions

| ID | Question | Impact | State |
| --- | --- | --- | --- |
| **Q01** | Is a Workspace ONE UEM tenant with API access available for live verification? *(asked 3×)* | ⛔ Blocks E04-T02 and E04-T04. Assumption in force: **not available — proceeding mock-first** | open |
| **Q02** | Is a second UEM tenant available, so fan-out can be tested for real rather than simulated? | Verification depth of E06 | open |
| **Q03** | Is an App Volumes Manager instance with API access available? | Verification depth of E05 | open |

**Resolved:** Q04 → MIT (D16) · Q05 → repository URL (D17) · Q06 and Q07 → renamed to PackForge4UEM (D18)

## Risks

| ID | Risk | Severity | Mitigation |
| --- | --- | --- | --- |
| **R01** | The Freestyle workflow JSON schema and its import API surface are unverified; historically Freestyle exposed less through the API than through the console | 🔴 high | Mock-first development behind an interface; verify against a live tenant as soon as one exists; treat the generator as replaceable |
| **R02** | Four weeks to deadline for three components and two external integrations | 🔴 high | Narrow MVP (D14); OpenAPI contract frozen in W1 to unblock parallel work; launch content treated as a deliverable, not an afterthought |
| **R03** | `SQLITE_BUSY` under concurrent publish fan-out | 🟠 medium | WAL mode plus serialised writes from the start (D04); load test the fan-out path in W3 |
| **R04** | Large artifact uploads failing mid-transfer | 🟠 medium | Chunked resumable upload with hash verification; a dropped connection must not discard a completed build |
| **R05** | Credentials for multiple UEM tenants concentrated in one service | 🟠 medium | Secrets encrypted at rest, never returned through the API, audit log on every use; mTLS for agents (D07) |
| **R06** | Trademark confusion from the project name and from referencing Omnissa product names | 🟡 low | Explicit non-affiliation disclaimer in the README; resolve Q06 before publication |
