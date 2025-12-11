# Invoice Processing — Cleaned Workflow

## Summary
Deterministic pipeline: detect incoming invoice emails, extract a constrained set of fields, validate with rule-based checks, and only write to ERP after verification.

## Who
For Finance and IT teams that require auditable, low-risk invoice ingestion into ERP systems.

## Workflow (4 steps)
1. Trigger / Ingest
   - Email receiver captures incoming messages and stores attachments as document records.
   - Semantic trigger identifies invoices using natural-language cues (e.g., "invoice", "PO", "bill").

2. Extract (Document LLM)
   - Document LLM performs layout-agnostic parsing and outputs a structured JSON with fixed keys:
     Vendor Name, Invoice Date, Line Items, Total Amount, PO Number.
   - Fields that cannot be parsed are marked incomplete (never guessed).

3. Validate (Rule-based Agent)
   - Agent performs deterministic checks (no LLM inference):
     • PO lookup against ERP/procurement system.
     • Numeric checks (amounts, date validity).
     • Amount threshold gating (e.g., > configured limit → require review).
   - Any failed check flags the invoice for human review.

4. Post / Notify
   - If all checks pass: normalized record is written to ERP via controlled API and logged.
   - If any check fails: route extracted data + original PDF + reason to human-in-the-loop (Slack/Teams) for approve/decline.
   - Maintain full audit trail with timestamps and user IDs.

## Implementation notes
- OCR fallback for scanned PDFs before LLM extraction.
- Retry policy: 3 attempts for transient extraction errors.
- Logging and audit must be immutable for compliance.
- Keep the LLM responsibility strictly to extraction; decision logic must be non-LLM.
