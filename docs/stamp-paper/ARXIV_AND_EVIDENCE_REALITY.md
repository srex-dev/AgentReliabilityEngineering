# arXiv vs this repo — short pointer

**Start here instead of guessing:** [`paper/README_PAPER_PIPELINE.md`](README_PAPER_PIPELINE.md)

## In one paragraph

- **“arXiv-ready” here** means the **argument** is structured for submission (abstract, STPA closure, appendices, figures as PNG, evidence **tiering** in §13) — **not** that we ship a default **LaTeX** build. arXiv `cs.*` often expects **TeX**; we currently use **Markdown → Word → PDF**.  
- **Evidence is tiered, not binary:** public paper + **`EVIDENCE_PUBLIC_SUMMARY.md`** (attestation) + **full hashed bundle** on request or as supplementary (`assemble_submission_package.py`). The public PDF omits raw logs on purpose — see §13.  
- **Formatting / figures:** run `render_paper_assets.py` before `md_to_arxiv_docx.py`; polish in Word or port to LaTeX for camera-ready — [`PDF_AND_WORD_FORMATTING.md`](PDF_AND_WORD_FORMATTING.md).

## Links

| Topic | Doc |
|-------|-----|
| Full bundle path + integrity | `research/evidence-bundles/README.md` |
| Public one-page attestation | `paper/EVIDENCE_PUBLIC_SUMMARY.md` |
| Word/PDF quirks | `paper/PDF_AND_WORD_FORMATTING.md` |
| Local compose vs synthetic vs staging (T0–T3) | `docs/validation-tiers.md` |
