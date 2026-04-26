# Public research mirror

The **ARE** implementation repository (`srex-dev/are`) is **private**. This folder holds a **public copy** of the normative **STPA / STAMP** working package so reviewers and readers can read closure artifacts without repository access.

## Contents

| Path | Role |
|------|------|
| [`stpa/`](stpa/) | Mirror of `are/research/stpa/` — `STPA_RESOLUTION.md`, `STPA_PACKAGE.md`, UCA enumeration, hazard closure, boundaries, causal scenarios |

## Sync (maintainers)

From a checkout that has both repos, after editing STPA files under `are/research/stpa/`:

```bash
cp are/research/stpa/*.md AgentResponsibilityEngineering/research/stpa/
# then commit + push AgentResponsibilityEngineering
```

Bump **`stpa/MIRROR_SYNC.txt`** (optional) with ISO date when you sync.

## Normative reference

For the bounded safety-case paper, **`stpa/STPA_RESOLUTION.md`** is the closure record (“STPA complete within the defined execution boundary”). The frozen evidence **bundle** (logs, hashes) remains with the private platform repo or supplementary zip.
