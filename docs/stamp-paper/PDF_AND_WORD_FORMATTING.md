# PDF and Word formatting — known limits and fixes

## Why the PDF can look “wrong”

The slim paper is authored in **Markdown**, then converted with a **small in-repo script** (`tools/paper/md_to_arxiv_docx.py`) into **Word**, then exported to **PDF** (Word, `docx2pdf`, etc.). That path is **not** LaTeX: you do **not** get IEEE/ACM default typography, floating figures, or automatic equation numbering unless you add them in Word or move to **TeX**.

**Implication:** For a **venue-grade** PDF, plan either:

- **Hand polish in Word** once (styles, page breaks, caption numbering), or  
- **Port to LaTeX** (separate work item; see `ARXIV_AND_EVIDENCE_REALITY.md`).

## What the converter does today

- Headings `#` / `##` / `###` / `####`  
- **Bold** `**…**` in body text  
- **Markdown tables** → Word “Table Grid”  
- **Images** `![caption](assets/…)` → centered embedded PNG + italic caption  
- **Bullets** `- ` and **numbered** `1. ` lists  
- It does **not** split giant single-line paragraphs from the legacy Word extract — break those manually in Markdown if they render as dense blocks.

## Figures and “equation” images

Run **`python tools/paper/render_paper_assets.py`** before `md_to_arxiv_docx.py` so `paper/assets/*.png` exists. The manuscript references:

- `assets/fig_golden_path.png`  
- `assets/eq_ordering.png`  
- `assets/eq_allow_ledger.png`  

## Example: Desktop PDF path (Windows)

After regenerating `paper/STAMP_ARE_ARXIV_READY.docx`:

```bash
python tools/paper/md_to_arxiv_docx.py "C:/Users/jonat/OneDrive/Desktop/STAMP_ARE_Paper.docx" paper/STAMP_ARE_ARXIV_READY.md
```

Open the DOCX in Word → **File → Save As → PDF** → e.g. `C:\Users\jonat\OneDrive\Desktop\STAMP_ARE_Paper.pdf`.

Or use `docx2pdf` if Word automation is available:

```bash
python -c "from docx2pdf import convert; convert(r'...STAMP_ARE_Paper.docx', r'...STAMP_ARE_Paper.pdf')"
```

## Public discipline repo

Sync the regenerated `.md` / `.docx` / `.pdf` to [AgentResponsibilityEngineering](https://github.com/srex-dev/AgentResponsibilityEngineering) when you want the public PDF to match the `are` repo state.
