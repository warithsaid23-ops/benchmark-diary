# Deployment and verification

## Current configuration

- GitHub repository: https://github.com/warithsaid23-ops/benchmark-diary
- Form endpoint: https://script.google.com/macros/s/AKfycbwQcIbkY1Hq6Hb7sEQp23Q6Uv6GQPMOlh59VLKyA5BhIGaICtXFXwMfg4GeOPe4ow8nnw/exec
- Sheet: https://docs.google.com/spreadsheets/d/1lYI4ipYtNYHAP-ke47cOa0aQL9n52jtPQtxPVJKT6zc/edit
- Sheet tab gid: 0
- Published CSV: https://docs.google.com/spreadsheets/d/e/2PACX-1vTS17qI7XxT_8dNh_h-L55ettk2nhbYAB4ECHMcEiHDIZnTHY1sxHUkg8AYb9rGotPrMQDGU_gH1fCK/pub?gid=0&single=true&output=csv

## Backend

The backend deployment source is kept in the administrator working copy. It writes to the configured Sheet, creates or repairs the 33-column header, validates required fields, assigns a permanent submission ID, and deduplicates by exact ID under a script lock. It records email status after the row is saved.

In the bound Apps Script project:

1. Set BEL_SPREADSHEET_ID and BEL_SHEET_ID in Project Settings → Script properties.
2. Save the code.
3. In Deploy → Manage deployments, edit the existing Web app deployment and deploy a new version.
4. Keep its current access and execution settings so the public form can submit.
5. Open the /exec URL without form parameters and confirm the JSON health response.

A submission acknowledgement is accepted only when it comes from the current Apps Script iframe and carries the matching report ID and one-time nonce. The frontend accepts only the production site origins:

- https://benchmarkengineering.co.tz
- https://www.benchmarkengineering.co.tz
- https://warithsaid23-ops.github.io

Local file previews deliberately do not transmit.

## Frontend

Publish these files together:

- index.html
- ceo.html
- admin.html
- manifest.json
- sw.js
- icon-192.svg
- icon-512.svg
- the four role guides

Keep the endpoint constants in index.html and admin.html aligned with the current /exec URL. Never store the endpoint in browser storage.

## Controlled live check

Use a clearly labelled test report. Verify that the backend creates exactly one row, assigns a submission ID, and records the email status. Check the four recipient calls and the dashboard record. A saved row with partial, failed or unknown email status must not be resubmitted to repeat mail.

Then inspect the form by refreshing and using browser back/forward, validate that one required field is highlighted at a time, and verify that an offline report is queued and submitted after reconnection. Photos remain local to the draft until a storage service is connected.

