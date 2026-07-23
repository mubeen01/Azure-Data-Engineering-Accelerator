<!--
Draft content for the GitHub Release titled "v1.0.0" — paste this into the
release description box at github.com/mubeen01/Azure-Data-Engineering-Accelerator/releases/new.
Tag suggestion: v1.0.0, targeting main (already committed and pushed).
This file is a draft, not a permanent part of the repo's documentation —
CHANGELOG.md remains the authoritative running history; see its
`## [1.0.0] - 2026-07-23` entry for the full, dated changelog this
release note summarizes.
-->

# Azure Data Engineering Accelerator v1.0.0

A reusable, end-to-end framework for building Azure data platforms: SQL
warehouse, Azure Data Factory, Databricks, Microsoft Fabric, Azure
Synapse, Infrastructure as Code, monitoring/security, real CI — plus
**three** complete industry accelerators (Banking, Healthcare, Retail)
proving all of it actually fits together, not just one.

## Highlights

**SQL Framework** (`src/sql/`) — a full star schema: 7 dimensions with
SCD Type 1 and Type 2 support, 3 fact tables, staging landing tables,
metadata-driven ETL (watermark pattern, audit logging, data quality
validation), columnstore indexing, and Synapse dedicated-pool
compatibility notes throughout.

**Synthetic Data Generator** (`tools/synthetic-data-generator/`) — a
configurable Python tool that generates realistic sample data instead of
committing static CSVs. Generate 1,000 rows for a quick test or 1,000,000
for load testing with one parameter. Banking, Healthcare, and Retail
domains fully implemented; Insurance is the one domain still open.

**Azure Data Factory Framework** (`src/adf/`) — metadata-driven, not
hand-built per table: a control table drives a generic orchestrator that
branches between full and incremental (watermark-based) load pipelines,
with retry policies and webhook failure notification. Every industry
accelerator plugs into this the same way — a metadata seed script, never
a new pipeline.

**Databricks Medallion Framework** (`src/databricks/`) — Bronze (Auto
Loader), Silver (cleanse/conform), Gold (the same star schema as the SQL
path, via Delta Lake `MERGE` — including the NULL-merge-key trick for SCD
Type 2), a streaming variant, and OPTIMIZE/ZORDER/VACUUM maintenance.

**Infrastructure as Code** (`src/infrastructure/`) — the full resource set
in both Bicep and Terraform, kept functionally identical: storage, Key
Vault, SQL, Data Factory, Databricks, Log Analytics, and the RBAC wiring
between them. Zero errors/warnings on `az bicep build` and
`terraform validate`.

**Monitoring & Security** (`src/monitoring/`, `src/security/`) —
diagnostic settings and alerting layered on top of the infrastructure
above (not duplicating it), Entra ID admin access, a Databricks Access
Connector for Unity Catalog, and an opt-in CI/CD app registration using
GitHub Actions OIDC — no stored secrets.

**Microsoft Fabric** (`src/fabric/`) — a Fabric Warehouse compatibility
document (the one non-negotiable gap from `src/sql/`: no `IDENTITY`
columns, documented with the `SEQUENCE`-based rewrite), Lakehouse
notebooks adapted from `src/databricks/`, and a Fabric Data Factory
pipeline chaining Bronze → Silver → Gold. Originally deferred
post-v1.0 — built anyway, since the same framework adapts cleanly.

**Azure Synapse Analytics** (`src/synapse/`) — a serverless SQL Pool
layer querying Gold Delta tables directly via `OPENROWSET ... FORMAT =
'DELTA'` (no data movement), Spark pool notebooks adapted from
`src/databricks/`, and one pipeline using the `SynapseNotebook` activity.
Also originally deferred post-v1.0 — same story as Fabric.

**Real CI** (`.github/workflows/ci.yml`) — 5 jobs: Bicep, Terraform
(matrixed across all 3 IaC configs), JSON well-formedness for every
ADF/Fabric/Synapse pipeline, Python syntax for every notebook plus a live
smoke-test of the generator, and a real SQL Server 2022 service container
running every script in `src/sql/` and all three accelerators. Also
originally deferred — built anyway.

**Three Industry Accelerators** — the actual proof the framework
generalizes, not just one data point:
- `examples/banking/` — the first, and the one that surfaced the
  framework's real early gaps (a generator path bug, a missing
  ADF load-order guarantee). Two new dimensions (`dim_account`,
  `dim_loan`) layered onto the generic model via FK.
- `examples/healthcare/` — the opposite extreme: *nothing* reuses a
  generic table (`dim_patient`, `dim_provider`, `fact_claims`,
  `fact_pharmacy` are all new), since healthcare has no generic-model
  equivalent at all. Hit the same embedded-address reconciliation
  banking's customer already solved — confirming that's a real,
  recurring generator shape.
- `examples/retail/` — the other extreme: `dim.dim_customer`,
  `dim.dim_product`, and `fact.fact_orders` are all *reused generic
  tables, unmodified*. Only `fact.fact_inventory` is new. Table reuse
  didn't mean procedure reuse, though — surfaced that the framework's
  own generic staging tables have never actually been usable by any
  real pipeline in this repo.

Each accelerator has `sql/`, `adf/README.md` (metadata-driven — no
per-industry pipeline JSON), `databricks/` (a 12-task bundle + bespoke
Gold fact notebooks), `README.md`, and `architecture.md`.

**Documentation** — `docs/` now has 8 files (getting-started,
installation, architecture, best-practices, coding-standards,
naming-conventions, faq, troubleshooting), `architecture/` has 4 design
docs (overview, medallion, deployment, security), and every module under
`src/`, `tools/`, and `examples/` has its own README.

## What's verified vs. what's reviewed

This release is honest about its own verification status rather than
overclaiming:

- **SQL — fully verified, not just reviewed.** Every script in
  `src/sql/` and all three accelerators (`examples/{banking,healthcare,retail}/sql/`)
  ran with zero errors against a real, live SQL Server 2022 container.
  Beyond DDL: the real generated CSVs were loaded into staging and every
  load procedure ran end-to-end, with exact row-count matches and zero
  orphaned foreign keys on every fact table. This also proves (not just
  asserts) that retail's customers/products genuinely coexist with
  banking's in the same shared `dim.dim_customer`/`dim.dim_product`
  tables. See `CHANGELOG.md`'s `[1.0.0]` → Verified section for the full
  detail, including a local `sqlcmd`/`QUOTED_IDENTIFIER` gotcha found and
  documented (`docs/troubleshooting.md`) along the way.
- **Bicep/Terraform**: every template offline-validates cleanly. None has
  been applied against a real Azure subscription yet.
- **Databricks**: notebooks and all three accelerators' Asset Bundles are
  written and reviewed; none has run against a real Spark/Databricks
  cluster.
- **ADF**: pipeline JSON is structurally validated; none has run against a
  live Data Factory. (Note this is now the one gap in the pipeline — the
  SQL every ADF pipeline ultimately calls has been proven live, but ADF
  itself hasn't been.)
- **Fabric/Synapse**: notebook Python syntax and pipeline JSON are valid;
  neither has run against a live Fabric or Synapse workspace — no
  offline validation tool exists for either the way `az bicep build`/
  `terraform validate` exist for IaC.

See `docs/troubleshooting.md` and each module's README for specifics.

## What's not in this release

- The Insurance accelerator — blocked on its synthetic data generator not
  existing yet (no dataset shape defined anywhere, unlike the other three
  domains, which each had at least an empty placeholder CSV to design
  against). Tracked for a future release; see
  `.github/GOOD_FIRST_ISSUES.md`.
- Any real Azure deployment. Every Bicep/Terraform/ADF/Databricks/Fabric/
  Synapse artifact is written and offline-validated but none has touched
  a real subscription, workspace, or cluster.

Full detail in `ROADMAP.md`'s "Current status at a glance" section.

## Getting started

`docs/getting-started.md` has four paths in, including a Docker-only path
(now the most proven path in the repo — see "What's verified" above)
that needs no Azure subscription at all.

## Thanks

This is a v1.0.0 baseline, not a finished product — issues, discussions,
and PRs are welcome. See `CONTRIBUTING.md`.
