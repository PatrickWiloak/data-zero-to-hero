# TODO

Working task list for **data-zero-to-hero** (Data Zero to Hero, https://data.noblerworks.com/). Read this at the start of a work session and keep it current as work completes - check items off with a date, add follow-ups as they surface. History lives in git and CHANGELOG.md.

---

## Split (2026-10-21)

- [x] 2026-10-21 Split from cloud-data-ai-security-zero-to-hero: data scope, cyan accent, new IndexNow key, Data from Scratch path, family cross-links (CHANGELOG 2026-10-21).
- [ ] Not deployed: push to `main` deploys via docs-site.yml once the GitHub repo and Pages exist. Pages custom domain `data.noblerworks.com` + DNS CNAME are outside this repo.
- [ ] After first deploy: `python3 .github/scripts/check-repo-metadata.py --fix` (description + topics; needs admin), then `--check` passes in CI.
- [ ] 2026-10-28 Patrick: upload `assets/brand/social-preview-1280x640.png` at GitHub Settings > General > Social preview (no API). Card is the shared Zero to Hero template (`assets/brand/social-preview.html`, 2026-10-21).
- [ ] After first deploy: `https://data.noblerworks.com/<key>.txt` serves the key in `.github/site/indexnow-key.txt`; the deploy's "Notify IndexNow" step says `accepted` (a first-run 403 is normal; rerun `notify-indexnow.py --all`).
- [ ] Patrick, after first deploy: Search Console `sc-domain:noblerworks.com` -> submit `https://data.noblerworks.com/sitemap.xml`.
- [ ] `learn/glossary.md` and `learn/youtube.md` are still cloud/AI-shaped: add data terms and data channels, or trim to data.

## Patrick

- [ ] **Decide** whether the bio should carry a cert count again. If yes, add a `{{personal_certs}}` token in `build-site.py` fed from one checked-in value.
- [ ] 2026-11-05: Database Specialty lapses - `README.md` (top quote and About the Author) and `learn/README.md` bio go from 19x / 14x AWS / 64 to **18x / 13x / 63** (source `go-to-market/claims.md`).
- [ ] Distribution: one useful page each to r/dataengineering and r/SQL, and awesome-data-engineering, linking back.

## Exam currency (from the 2026-10-16 vendor sweep, data exams only)

Confidence in brackets: H = vendor page read, M = vendor page plus search, L = third-party.

- [ ] Judge 2026-11-01: Azure DP-420 renamed Cosmos DB AI Developer Associate (effective 2026-10-06, AI/RAG scope) [H]; DP-600/DP-700 (10-19) and DP-300 (10-27) content updates [H] - rebuild or date each.
- [ ] Snowflake codes: COF-C02 -> COF-C03 [M], ADA-C01 -> ADA-C02 [H], Advanced Data Engineer code looks wrong (catalog lists DEA-C02) [H]. Confirm, then fix.
- [ ] Databricks Lakehouse Platform Administrator is now an accreditation [M] - confirm and relabel.
- [ ] DP-203 is retired: decide whether its guide, practice bank and `labs-by-cert` entry stay as reference or go.
- [ ] By 2026-10-31: spot-check retired-product replacements - QLDB notes in `exams/aws/specialty/database-dbs-c01` and `exams/aws/shared/services/database/qldb.md`, and the AWS Data Analytics specialty notes.
- [ ] "Vertex AI" still appears in `exams/gcp/data-engineer` (renamed Gemini Enterprise Agent Platform 2026-04-22). Sweep with Google's rename table once names settle.
- [ ] New-guide candidates (verify codes first): Databricks Data Analyst and Apache Spark Developer, Google Associate Data Practitioner, Confluent CCAC, MongoDB Associate Data Modeler [L]. Snowflake SPS-C01 retires 2026-11-01 and SEA-C01 / DAA-C01 2026-12-01 [M]; no guides here.

## Tooling (shared with the sibling sites)

- [ ] 🟡 `.github/scripts/site_hooks.py` has drifted from ai-zero-to-hero's port - one canonical module the sites vendor (with a sync check), or record the fork. S-M, low.
- [ ] 🟡 `glossary-add-anchors.py`, `glossary-autolink.py`, `glossary-upgrade-existing-links.py` are a documented manual pipeline (`.github/AUTOMATION.md`, CONTRIBUTING); delete only if that pipeline is retired.
- [ ] Fold heading-anchor validation into `check-internal-links.py`, so a broken anchor is reported without a full site build.
- [ ] No CI check for unbalanced code fences; the site build only catches them as missing anchors. A direct validator would name file and line.
- [ ] markdownlint MD060 (table-column-style) fires repo-wide: disable it in `.markdownlint.json` or reformat every table.
- [ ] Judge after the first Monday link-check run on this repo: it opens or updates one `link-check` issue (not a new one each week) and lists no real 404s.
