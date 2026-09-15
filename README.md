# Benchmark Engineering Daily Site Diary

Bilingual English/Swahili mobile-first site diary for Benchmark Engineering Limited.

Live pages:

- Engineer form: https://warithsaid23-ops.github.io/benchmark-diary/
- CEO dashboard: https://warithsaid23-ops.github.io/benchmark-diary/ceo.html
- Admin panel: https://warithsaid23-ops.github.io/benchmark-diary/admin.html
- Diary Sheet: https://docs.google.com/spreadsheets/d/1lYI4ipYtNYHAP-ke47cOa0aQL9n52jtPQtxPVJKT6zc/edit

The form uses one hidden iframe POST to Google Apps Script. The endpoint is hardcoded in index.html; browser storage never overrides it. Each report receives a permanent submission ID, and the Apps Script backend uses durable PropertiesService and a sheet-column check to prevent duplicate rows.

The form saves drafts as the engineer types, restores them after reload, stores compressed photos locally for the current draft, and queues a validated report while offline. When the connection returns, the queue submits the frozen report once and waits for the backend acknowledgement before clearing it. Photos are retained in the phone draft and still need to be sent to the project team separately until photo storage is connected.

The dashboard reads the published Sheet CSV, caches the last successful view for offline reading, and escapes stored text before displaying it. The admin page is a read-only configuration view; changing an endpoint requires editing the hardcoded source and publishing it.

## Backend release

The Apps Script deployment source is kept in the administrator working copy and is applied to the project bound to the diary Sheet.

1. Open the Sheet and choose Extensions → Apps Script.
2. Replace the project source with Code.gs, preserving unrelated project files and triggers unless they are intentionally retired.
3. In Project Settings, set:
   - BEL_SPREADSHEET_ID = 1lYI4ipYtNYHAP-ke47cOa0aQL9n52jtPQtxPVJKT6zc
   - BEL_SHEET_ID = 0
4. Save, then edit the existing Web app deployment and deploy a new version so the current /exec URL remains stable.
5. Confirm the endpoint health response contains {"status":"ready"} before testing a diary.

The backend expands the record to 33 columns. Columns AE, AF and AG are Submission ID, Email Status and Payload Hash. Do not delete or reorder those columns.

Notification recipients remain configured inside the private Apps Script project. Email is attempted only after the Sheet row has been written; a retry never sends the email again.

## Verification

Local regression coverage includes backend deduplication, locking, validation, safe HTML, iframe acknowledgement, offline queue behavior, draft restoration and dashboard CSV handling. A real deployment test should use a clearly labelled test diary, then verify one new Sheet row, its permanent ID, email status and the dashboard record.

