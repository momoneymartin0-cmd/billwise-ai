# BillWise AI — Full-stack launch candidate

A working self-hosted South African electricity-invoice screening web application. Unlike the early prototype, this edition includes real sign-up/login, server-side SQLite persistence, organisation isolation, site management, PDF storage/extraction, configurable tariffs, invoice review statuses, audit activity and CSV reports. It **does not** constitute an accredited electricity tariff audit or a production security certification.

## Run locally (VS Code)

1. Install **Node.js 22.13+**. On Linux install `poppler-utils` (`pdftotext`) to extract searchable PDF text. On Windows, install Poppler's `pdftotext.exe` on PATH if you need PDF text extraction; uploads otherwise still work with manual entries.
2. Extract this ZIP, open its `billwise-launch` folder in VS Code.
3. From VS Code terminal, run `npm start` — **no npm install needed**.
4. Visit `http://localhost:3000` and click **Create account**. Your password must contain at least 12 characters.
5. Select **Load fictional sample data** if you want demo dashboards or create your own properties and records.

To choose a port, set `PORT=8080` in your environment. To choose storage location set `DATA_DIR=/absolute/persistent/path`. Default storage is `./data/billwise.sqlite` plus the associated `./data/uploads`. Back up this entire directory; **never commit it to a repository**.

## What works

- Company registration with salted scrypt password hashes and HttpOnly session cookies
- Separate organisation-scoped property, tariff and invoice data
- Property creation and editing, provider and utility account records
- Text-based PDF upload (6 MB), private storage and best-effort extraction using `pdftotext`; user confirms all amounts
- Manual invoice entry, review status and notes, delete, secure PDF download
- Configurable site-specific unit / standing / demand allowances / VAT / tolerance and document source
- Screening calculations after an organisation approves a rule; flags indicate *potential* discrepancies only
- Overview cards, spending chart, downloadable CSV, 40 most recent audit events
- Optional **fictional** sample records for new organisations

## Not yet included / blocking full commercial launch

- Not a substitute for licensed utility billing expertise. Tariff inputs are customer-entered and the current formula **does not** handle tiered tariffs, time-of-use, maximum demand intervals, fixed daily fees, seasons, municipal levies, pro-rating, meter calibration, rebates, or complex invoice line items. Rules have a single effective date rather than historical version selection.
- OCR of image-only PDFs is not supported; text extraction is best effort and its numeric suggestions must be reviewed.
- No email verification, password reset, MFA, invitation / multi-user administration, payment subscription or integrated LLM. These need a secure provider integration before public launch.
- Public sign-up and application rate limiting are basic. Before public deployment, obtain a security review, stronger abuse prevention, session management and recovery flows, monitoring, alerting, upload malware screening, disaster recovery tests and documented access governance.
- Need POPIA privacy notice, retention and deletion procedures, operator arrangements, infrastructure/security assessment, separate staging environment and formal financial correctness acceptance tests.

## Deployment architecture

Use Node.js 22+ behind an HTTPS reverse proxy (Caddy or nginx). Run as an unprivileged service in `NODE_ENV=production`; preserve `DATA_DIR` on encrypted persistent storage and restrict filesystem permissions. Do NOT host writable uploads in `public/`. Configure backups, monitoring, HTTPS, application-firewall policies, secure secrets and restore tests. For larger deployments, migrate from local SQLite to a managed database, external object storage, job queue and production-grade identity provider.

Example local service:

```bash
npm start
```

The production server is pure Node standard library, with Node's built-in SQLite module. No third-party JavaScript package is required. PDF text extraction invokes `pdftotext` as an optional system utility.

## Test checklist

- Register two separate organisations. Confirm that neither can view, edit, export or download the other's invoices.
- Log out, log in again; test wrong passwords and expired session behaviour.
- Add a property and create an invoice manually.
- Create tariff rules with a documented source, approve them and compare expected results against hand-calculated values.
- Upload a searchable PDF and an image-only PDF; manually verify extracted fields before saving.
- Review an invoice; export CSV and test with leading spreadsheet-control characters in references.
- Test HTTPS, persistent storage restarts, backup restoration and penetration/security reviews before real customer use.

Copyright 2026. Source code delivered for user modification; no external service accounts or regulatory approvals are included.
