# Data Zero to Hero - Project Instructions

## Overview
A free learning site for data engineering, analytics engineering and database administration - plain-English concepts, the Data from Scratch path, hands-on builds, architecture patterns, and certification prep for AWS, Azure, GCP, Databricks, Snowflake, Confluent (Kafka) and MongoDB data exams. Cert and provider counts live in `docs/certs.json` (`totals`), not here - don't type them into this file. Certifications are one pillar; the site also serves non-cert learners.

One of four sibling sites (the Zero to Hero family, all by Nobler Works): [Cloud](https://cloud.noblerworks.com/) (`~/coding/cloud-zero-to-hero`), **Data** (this repo), [Security](https://security.noblerworks.com/) (`~/coding/security-zero-to-hero`), [AI](https://ai.noblerworks.com/) (`~/coding/ai-zero-to-hero`). Out-of-subject content lives on the sibling and is linked by absolute URL, never copied back. History: split from cloud-data-ai-security-zero-to-hero on 2026-10-21.

## Structure
```
data-zero-to-hero/
├── exams/              # Cert-specific study guides (the certify pillar)
├── learn/              # Plain-English learning content (the learn pillar)
│   ├── concepts/       # Bite-size topic pages (5-10 min): data primitives
│   ├── data-from-scratch.md  # The zero-to-data-role path; linked from README, STUDY-HUB, home
│   ├── glossary.md
│   └── youtube.md
├── resources/          # Cross-cert reference (build + reference pillars)
├── topics/             # Cross-pillar topic indexes (databases, data engineering)
├── docs/               # Repo-level docs (ARCHITECTURE.md, certs.json, freshness.md, tag-taxonomy.md)
├── .github/site/       # Site chrome: extra.css, home.md (landing page), indexnow-key.txt
├── mkdocs.yml          # Site config; nav is generated, not written here
├── README.md           # Top-level overview
├── STUDY-HUB.md        # Navigation hub
└── CONTRIBUTING.md     # How to contribute
```

## Purpose / Usage
- Study notes and exam prep for data certifications (cert pillar)
- Plain-English data learning for non-cert students (learn pillar)
- Reference: data architecture patterns, cross-cloud database and messaging comparisons, roadmaps, migration and interview guides (build + reference pillars)
- Markdown-based knowledge base; `STUDY-HUB.md` is the navigation hub.
- **Also published as a website**: <https://data.noblerworks.com/>, built by `.github/workflows/docs-site.yml` on push to `main`. The site is generated from the markdown as-is - **never restructure content or add frontmatter to satisfy the site build.** Site-only fixes go in `.github/scripts/build-site.py`, which transforms a staged copy in `.site-src/` and never touches the repo's markdown. Theme colour lives in `.github/site/extra.css` (near-black plus the **cyan** accent - `#0891b2`, `#0e7490` for text on white - each family site has its own accent; `mkdocs.yml` sets `primary: custom` so Material's palettes are bypassed).
- **Family bar:** the top announce block in `.github/site-overrides/main.html` and its CSS at the end of `extra.css` are identical across all four sites - edit all four repos together or not at all. The "which site?" page (`learn/which-site.md`; ai: `learn/start-here/which-site.md`) is also byte-identical in all four - edit all four together.
- **The site's home page is `.github/site/home.md`, not `README.md`.** A repo front page (banner, badges, repo structure, "star this repo") and a website landing page want different things, so the build renders `home.md` over the staged `README.md`. Editing the README does not change the site's front door - except for the "What's new" bullets, which are extracted from it. Every number on that page is a `{{token}}` filled from `certs.json` and `check-readme-counts.py`; never type a figure into it. See [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md#the-home-page-is-not-the-readme).
- **Site URL lives in `mkdocs.yml` `site_url` only.** `notify-indexnow.py` and the robots.txt step derive from it. `.github/site/indexnow-key.txt` is this site's own IndexNow key - never copy a sibling's.
- Organized by purpose (learn / certify / reference) and within each, by provider.
- Each cert dir has: `README.md`, `fact-sheet.md`, `notes/`, `practice-plan.md`, `scenarios.md`, `strategy.md`.

## House style / conventions

### House style
- No em dashes (-). Use regular dashes (-) only.
- Plain English, short sentences. Avoid emoji in body text (section markers OK).
- Cite vendor docs, don't paraphrase. Use the `**[📖 Title](URL)** - description` link format.
- No verbatim vendor exam questions.

### Visual content standards
- **Mermaid fenced code blocks are the default.** Write the diagram inline in the page that uses it. GitHub renders Mermaid natively, it stays editable in the markdown, and it diffs as text in review.
- Prefer `flowchart TB` / `flowchart LR` over the older `graph` syntax. Use `subgraph` for grouped components. Don't hard-code colours; the diagram has to read in both light and dark themes.
- Mermaid has no alt text, so give each diagram a caption or a sentence of prose saying what it shows.
- **PNG is the exception**, for diagrams too dense to read inline. Save to `assets/diagrams/<topic>/<slug>.png` (topic subdirs created lazily) and embed with descriptive alt text: `![3-tier architecture with load balancer, app servers, and database](../../assets/diagrams/architecture/web-app-3-tier.png)`
- See [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md#visual-content-standards) for the full convention.

### Frontmatter convention (new and refreshed pages)
```yaml
---
last-updated: YYYY-MM-DD
applies-to: AWS console as of 2026-Q2          # optional
difficulty: beginner | intermediate | advanced  # optional
reading-time: 10 min                            # optional
---
```
Backfill is opportunistic. Don't add frontmatter to thousands of files in one PR.

### Automation
- `.github/workflows/` - link-check (lychee, weekly + on PR), markdown-lint (markdownlint-cli2), structure-validate (custom scripts), docs-site (MkDocs build + Pages deploy; the build runs `--strict` as a blocking PR gate).
- `.github/scripts/validate-cert-structure.sh` - confirm every cert dir has a README; warn on missing fact-sheet, practice-plan, scenarios, strategy.
- `.github/scripts/build-freshness-ledger.sh` - regenerate `docs/freshness.md` from `last-updated` frontmatter. Run after meaningful content updates.
- `.github/scripts/build-site.py` - stage and build the site. `--serve` for live preview, `--strict` to fail on any broken link or anchor.
- See [docs/freshness.md](./docs/freshness.md) for the per-cert verification ledger.

### Counts are checked, not remembered
Every number advertised in `README.md` and `STUDY-HUB.md` is verified by CI. Cert and provider counts come from `docs/certs.json` via `build-certs-index.py --check`. The per-provider tables in both files are **generated** from that index by `build-provider-indexes.py`, between `<!-- BEGIN GENERATED: ... -->` markers - don't hand-edit inside a marked block. Everything else (concept pages, topic indexes, comparisons, patterns, projects, word count, doc-link floor, the Repository Statistics block) is verified by `check-readme-counts.py --check`.

**When you add or remove content, run `python3 .github/scripts/check-readme-counts.py --fix` in the same change.** Hand-kept counts drift, and a dated snapshot restated elsewhere reads as a current fact. If you add a new counted claim to the README, add a matching entry to `CLAIMS` in that script - an unchecked number goes stale.

The script counts via `git ls-files`, never a filesystem walk: a local site build leaves a full staged copy of the tree in `.site-src/`, and walking the working directory counts every page twice.

## Docs stay current
- Update README, `docs/`, this CLAUDE.md and TODO.md **in the same commit** as the change that makes them wrong, never in a later cleanup. A stale doc is a bug.
