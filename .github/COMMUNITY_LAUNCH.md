<!--
Draft content for two manual GitHub setup steps: enabling Discussions and
creating a Project board. Delete or archive this file once both exist.
-->

# Project Board

Suggested columns (GitHub Projects, "Board" view):

| Column | Contents |
|---|---|
| **Backlog** | Everything in `.github/GOOD_FIRST_ISSUES.md` not yet picked up |
| **Ready** | Issues with enough detail to start immediately — most of `GOOD_FIRST_ISSUES.md` qualifies as-is |
| **In Progress** | Actively being worked (link the branch/PR) |
| **In Review** | Open PR, awaiting review/merge |
| **Done** | Merged |

Suggested initial cards for **Backlog**/**Ready**, pulled directly from
`.github/GOOD_FIRST_ISSUES.md` (create the GitHub Issues first, then add
them as cards so the board and issue tracker stay linked):

1. Add the Insurance domain to the synthetic data generator
2. Build the Insurance accelerator end-to-end (depends on #1)
3. Reconcile the generic staging tables with ADF's generic Copy activity
4. Add a worked Databricks Gold notebook for `fact_orders`
5. Wire Unity Catalog's storage credential (needs a live metastore)
6. Add a security-specific Key Vault audit alert
7. Automate the dataset-to-storage-account upload step
8. Add a "prod" Bicep parameters file
9. Watch `validate-sql` actually run on GitHub's infrastructure

# Discussions

Suggested categories to enable: **Announcements** (maintainer-only),
**General**, **Ideas**, **Q&A**, **Show and tell**.

## Welcome post draft (pin to Announcements)

---

**Welcome to Azure Data Engineering Accelerator**

ADEA is a reusable framework for building Azure data platforms — a full
SQL star schema, metadata-driven ADF pipelines, a Databricks medallion
implementation, Microsoft Fabric and Azure Synapse support,
Infrastructure as Code (Bicep + Terraform), monitoring/security, and real
CI, all designed to work together rather than as disconnected samples.

**Where things stand at v1.0.0**: the core framework is built and three
complete industry accelerators (Banking, Healthcare, Retail) prove it
works end-to-end — and prove it *generalizes*, since each hit a different
flavor of "the generic framework doesn't just work as-is" (see each
accelerator's own README). Insurance is next, blocked only on its
synthetic data generator not existing yet (see
`.github/GOOD_FIRST_ISSUES.md` if you want to help close that gap).

**Verification status is documented, not hidden**: the SQL layer is
verified further than the rest — every script in `src/sql/` and all
three accelerators has actually run against a live SQL Server 2022
container, including a full staging-to-dimension/fact ETL pass with real
generated data (exact row counts, zero orphaned foreign keys). Every
Bicep/Terraform template offline-validates cleanly, but none has deployed
to a real Azure subscription yet; Databricks notebooks, ADF pipelines,
and Fabric/Synapse items are all written and reviewed but haven't run
against a live workspace either. Check `docs/troubleshooting.md` and
`ROADMAP.md` before assuming something works end-to-end just because
it's written — and if you run something that isn't listed as verified
and it works (or doesn't), that's exactly the kind of thing worth a post
here.

Use **Q&A** for "how do I..." questions, **Ideas** for anything not
already tracked in `.github/GOOD_FIRST_ISSUES.md` or the project board,
and **Show and tell** if you build a new industry accelerator or extend
the framework — that's the whole point of this being reusable.

---
