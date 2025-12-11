# RAW INPUT – Invoice processing (team notes)
- incoming email with PDF attachments
- need to detect invoice vs other email
- PDFs have messy layouts and variable formats
- required keys: Vendor Name, Invoice Date, Line Items, Total, PO Number
- want deterministic extraction, avoid hallucination
- need validation: PO lookup, amount threshold
- exceptions should route to human review (Slack/Teams)
- ERP must only get audited, validated writes
- retry and logging requirements unclear
