# arXiv submission checklist — STAMP / ARE preprint

Use this before uploading **STAMP_ARE_Paper.pdf** (canonical file at the [AgentResponsibilityEngineering](https://github.com/srex-dev/AgentResponsibilityEngineering) repo root) or depositing a LaTeX build. arXiv rules change; verify against [info.arxiv.org/help/submit](https://info.arxiv.org/help/submit/index.html) for your subject class.

## PDF

- [ ] **Fonts:** Open the PDF in a reader that shows font properties (e.g. Acrobat → File → Properties → Fonts). Prefer fully embedded subsets; avoid missing glyphs in headings or tables.
- [ ] **Page size:** Letter or A4 consistent; margins readable (target ~1" / 2.5 cm if using the Word template path).
- [ ] **Figures:** Raster figures (golden path, equation-style PNGs) are **≥ ~200 dpi** at printed size; no clipped tables.
- [ ] **File size:** Within arXiv limits for your category; compress images if needed without unreadable blur.
- [ ] **Hyperlinks:** If present, they resolve and are appropriate for a static preprint (no login-gated URLs as sole evidence).
- [ ] **Metadata:** Title and abstract on arXiv match the reconciled manuscript abstract in `STAMP_ARE_ARXIV_READY.md` (run `tools/paper/build_stamp_arxiv_reconciled.py` first).

## Ancillary / supplementary

- [ ] **Full evidence zip** is **not** required in the main PDF. Tiering is described in §13 of the paper and in `EVIDENCE_PUBLIC_SUMMARY.md`. Use **arXiv Ancillary Files** or venue supplementary material for the frozen packet when appropriate.
- [ ] **Hashes:** If attaching a bundle, include `FILES.sha256` (or equivalent) and a short `MANIFEST.md` as in `paper/submission-package/`.

## Build order (private `are` repo)

```bash
python tools/paper/render_paper_assets.py
python tools/paper/build_stamp_arxiv_reconciled.py
python tools/paper/build_reference_docx.py   # once, or when template margins/fonts change
python tools/paper/md_to_arxiv_docx.py
python tools/paper/build_stamp_arxiv_pdf.py  # see below
python tools/paper/sync_public_discipline_repo.py
```

### PDF quality (arXiv-style)

The **hosted** PDF is built with **LaTeX via pandoc** when possible: **10pt** body, **Latin Modern** (OpenType, the same design family as classic Computer Modern—what most arXiv `article` PDFs use). It is **not Times New Roman**; for **Times** you need a full local TeX install and **`pdflatex`** (the script passes `fontfamily=times` there). Docker’s `pandoc/latex` image often **cannot** build Times/Palatino `pdflatex` paths (missing Type1 metrics), so Docker sticks to **XeLaTeX + Latin Modern**.

1. **Local:** Install [Pandoc](https://pandoc.org/installing.html) and a TeX distribution (e.g. [MiKTeX](https://miktex.org/) on Windows). `build_stamp_arxiv_pdf.py` tries `pdflatex`, then `xelatex`, then `lualatex`.
2. **Docker (no local LaTeX):** Install [Docker Desktop](https://www.docker.com/products/docker-desktop/), then from the `are` repo root run  
   `python tools/paper/build_stamp_arxiv_pdf.py --docker-only paper/STAMP_ARE_Paper.pdf`  
   This pulls `pandoc/latex:latest` and uses **XeLaTeX** with **10pt Latin Modern**. Then run `sync_public_discipline_repo.py`.  
   *Tip:* Run `python …` from **cmd.exe** or **PowerShell** if Git Bash rewrites Docker `-w /data` paths.
3. **Fallback:** `docx2pdf` (Microsoft Word) only if pandoc paths fail — typography is weaker; the DOCX uses **10pt Times New Roman** for parity with the Word-first path.

## DOCX-only PDF path

If `build_stamp_arxiv_pdf.py` falls back to **docx2pdf**, enable **embed fonts** in Word when saving to PDF if your Word build offers it, and re-run the checklist above.
