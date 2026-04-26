# STAMP / ARE paper — one place to start

If anything felt **scattered**, use this file as the hub.

**Path note:** In the private **`are`** repo, this file lives at `paper/README_PAPER_PIPELINE.md`. In the public **`AgentResponsibilityEngineering`** mirror it is copied to **`docs/stamp-paper/README_PAPER_PIPELINE.md`**; manuscript sources are under **`paper/`**, figures under **`assets/stamp-paper/`**, and the canonical PDF stays at the **repository root** (`STAMP_ARE_Paper.pdf`).

## What to read

| Question | File |
|----------|------|
| **What is “arXiv-ready” vs LaTeX?** | `paper/ARXIV_AND_EVIDENCE_REALITY.md` (short) |
| **arXiv PDF deposit checklist** | `docs/stamp-paper/ARXIV_SUBMISSION_CHECKLIST.md` |
| **Read STPA without ARE access** | Public mirror: [AgentResponsibilityEngineering `research/stpa/`](https://github.com/srex-dev/AgentResponsibilityEngineering/tree/main/research/stpa) |
| **Why isn’t the full evidence bundle inside the public PDF?** | `paper/STAMP_ARE_ARXIV_READY.md` §13 + `paper/EVIDENCE_PUBLIC_SUMMARY.md` |
| **Word/PDF looks wrong — how do I fix formatting?** | `paper/PDF_AND_WORD_FORMATTING.md` |
| **Claims cheat sheet** | `paper/STAMP_ARE_FINAL.md` |

## Build order (slim paper → Word → PDF → public sync)

```bash
# 1) Figures / equation PNGs (matplotlib)
python tools/paper/render_paper_assets.py

# 2) Assemble Markdown (docx extract + STPA inserts + §13 + appendices)
python tools/paper/build_stamp_arxiv_reconciled.py

# 3) Reference Word template (margins, Normal/heading fonts) — once per machine / after template edits
python tools/paper/build_reference_docx.py

# 4) Word (embeds tables + images from paper/assets/; uses paper/templates/reference.docx when present)
python tools/paper/md_to_arxiv_docx.py

# 5) PDF — local pandoc (pdflatex/xelatex/lualatex) if installed; else Docker ``pandoc/latex``; else docx2pdf
python tools/paper/build_stamp_arxiv_pdf.py paper/STAMP_ARE_Paper.pdf
#    Docker-only (good typography without local TeX): python tools/paper/build_stamp_arxiv_pdf.py --docker-only paper/STAMP_ARE_Paper.pdf

# 6) Copy to AgentResponsibilityEngineering (sibling clone; or set AGENT_RESP_PUBLIC_REPO)
python tools/paper/sync_public_discipline_repo.py
```

Public layout mirrors **`docs/stamp-paper/`**, **`paper/`**, **`assets/stamp-paper/`**; **`STAMP_ARE_Paper.pdf`** stays at the **public repo root** for stable site links.

## Submission zip (manuscript + full bundle)

```bash
python tools/paper/assemble_submission_package.py
```

Output under `paper/submission-package/_dist/` (gitignored).
