# Bayhaus Privacy Guardrail — Repro (Aparavi DTC)

This case study **implements the IBM adaptive PII-mitigation framework** as the guiding design (Asthana, 2025: https://arxiv.org/abs/2501.12465).  
Goal: **no-code replication** — import one JSON, point to your `PDFs/` folder, run, and chat safely.

## Links
- Aparavi DTC: https://dtc.aparavi.com/
- Qdrant (vector DB): https://qdrant.tech/
- Google AI Studio (Gemini API key): https://aistudio.google.com/
- Pipeline JSON: PUT_YOUR_JSON_URL_HERE

## Repo layout
/ (repo root)
├─ PDFs/                 # 50 de-identified sample PDFs
└─ README.md
└─ pipeline.json

**Unit of evaluation:** document (PDF)  
**Mask character:** U+2588 `█` (keep consistent in outputs and figures)

---

## Step-by-step (about 5–7 minutes)

1) **Open Aparavi DTC** → *Pipelines* → **Import** → select the pipeline JSON (URL above).

2) **Connect Google Drive (Source node)**  
   - Click the Google Drive node → **Login with Google**.  
   - Set folder path to your Drive location containing the same `PDFs/` (e.g., `My Drive/chatbot/*`).  
   - **Save**.

3) **Parse to text (Parser node)**  
   - No change needed; this converts PDFs to text.

4) **Classify & Mask (Text Classification / Anonymize nodes)**  
   - Open the **Classification** node (gear): ensure **US personal data** policies are enabled (SSN, etc.).  
   - Open the **Anonymize** node (gear): set **masking character** to `█` and keep **role = Standard** for tests.  
   - Confirm that detected PII (e.g., SSN) is replaced by `█` blocks.

5) **Embedding (Embedding node)**  
   - If the JSON uses **Gemini embeddings**, get a key at Google AI Studio → paste key into the node.  
   - Keep chunk size/overlap as provided.

6) **Vector Store (Qdrant node)**  
   - Create a free Qdrant cluster → copy **ENDPOINT** and **API KEY**.  
   - In the Qdrant node, set **URL**, **API KEY**, and **Collection** (e.g., `chat_redacted_pii`).  
   - **Save**.

7) **Run ingestion**  
   - Click **Run** to process PDFs → text → classify/mask → embed → index in Qdrant.  
   - Check logs; ensure completion without errors.

8) **Chat**  
   - Open the Chat/HTTP Result node and click **Play**.  
   - Example safe prompt:  
     - *“What is the client name for ByteBay Studios?”*  
   - Expect a normal answer for non-PII (e.g., a client name present in the docs).  
   - PII (e.g., SSN) must remain **masked** (`███████████`) in any response.

---

## What to verify

- **Leaks:** **0/50** documents show PII in final chatbot answers.  
- **Over-masking:** Count documents where non-PII was masked (e.g., `3/50`).  
- **Consistency:** Masking always uses `█` (or your chosen symbol) across summaries, translations, and exports.  
- **Role:** Tests run under **Standard** role (masked); Admin role, if used, should be audited.

**Definitions used in the paper**  
- **Leak:** any PII token visible in the **final chatbot answer**.  
- **Over-mask:** non-PII text that was masked.

---

## Troubleshooting

- **Nothing is masked:** ensure policies are enabled and test role = **Standard**.  
- **Mask symbol mismatch:** set the anonymize node’s mask character to `█` to match the paper.  
- **Qdrant errors:** verify URL/API key/collection; cluster must be reachable.  
- **Embedding errors:** confirm the Gemini API key and model settings.

---

## Notes
- PDFs here are **synthetic/de-identified**.  
- This implementation follows the **IBM adaptive framework** (Asthana, 2025) for policy engine → contextual detection → adaptive remediation, realized end-to-end inside **Aparavi DTC** with **Qdrant** and **Gemini**.
