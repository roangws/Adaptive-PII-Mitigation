# Bayhaus Privacy Guardrail — Repro Pack (Aparavi)

Minimal, no-code reproducibility for our case study: **clone → import JSON → point to PDFs → run → export results**.

## What’s here
- `data/pdfs/` — the **50 PDFs** (de-identified/synthetic).
- `configs/aparavi_pipeline.json` — the **exact pipeline** used.
- `configs/policies_us.json` — the **policy/rule config** used.
- `results/metrics.csv` — exported **metrics** from our run.
- `version.txt` — Aparavi version/build + date.
- `docs/Runbook.md` (optional) — screenshots; or see **Video** below.

**Video walkthrough:** <ADD_UNLISTED_LINK_HERE>

---

## Prerequisites
- **Aparavi Data Toolchain for AI** account/workspace.
- Permission to import a pipeline and run jobs.
- (If your pipeline targets a vector store) **Qdrant** connector/credentials.

---

## Quick Start (5–7 minutes)

1. **Clone or download** this repo.  
   Keep the folder layout as-is.

2. **Open Aparavi** → **Import** `configs/aparavi_pipeline.json`.

3. **Set source** on the first node to this repo’s `data/pdfs/` folder.

4. **Load policies**: point the policy node to `configs/policies_us.json`.

5. **Masking character & roles**:  
   - Set redaction char to **U+2588 (█)** (or `*`, but be consistent).  
   - Ensure roles: **Standard** (masked), **Admin** (full view, audited).

6. **Vector store** (if present in your pipeline):  
   - Confirm **Qdrant** collection/URL/API key in the connector node.

7. **Run the pipeline** → after completion, **export**:  
   - Masked outputs, logs, and **metrics** (save to `results/`).

---

## What to report / verify

**Unit of evaluation:** **document (PDF)**.  
**Definitions:**  
- **Leak** = any PII token visible in the **final chatbot answer**.  
- **Over-mask** = **non-PII** text masked.  

Check your run against `results/metrics.csv` (or regenerate it):
- **Leaks:** expect `0 / 50` documents.
- **Over-masking:** e.g., `3 / 50` docs (~6%) *(update with your number)*.
- (If computed) **Precision/Recall/F1** on annotated spans.

> If your journal requires it, include screenshots or the exported CSV as supplementary material.

---

## Recreate our exact environment
- See `version.txt` for Aparavi build/date and any model IDs used.
- If your environment differs, note it in your submission.

---

## Troubleshooting
- **Nothing gets masked:** ensure `policies_us.json` is attached to the policy/anonymize node and the **role** is set to *Standard* for the test.  
- **Vector errors:** verify Qdrant credentials/URL and collection name in the connector node.  
- **Mask symbol mismatch:** set the masking character to **█** (or update the paper figures to match your chosen character).

---

## Ethics & Data
- PDFs are **de-identified/synthetic**; no real PII.  
- If you run a human trust survey, include consent text and IRB/ethics note in your paper.

---

## Cite
Please cite the case study and the IBM adaptive framework that conceptually guides this implementation.
