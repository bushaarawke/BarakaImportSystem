# Baraka Import System

Import management system for **Baraka Solar Manufacturing PLC** (Addis Ababa, Ethiopia), covering the full import cycle for goods supplied by Central Hub FZCO (Djibouti): order planning, trade documents, FCY and bank permit, and customs declarations with NBE delinquency tracking.

> **Keep this repository private.** `index.html` contains company details, the vendor bank account, TIN numbers, letterhead images and the vendor stamp.

## Modules

| # | Module | What it does |
|---|--------|--------------|
| 1 | **Order plan** | Products with per-container quantity, packages and weights; planned and additional orders with automatic order numbers (0001, 0002…); booking; COC before loading; ETA to Djibouti and Ethiopia; delay alerts; each container linked to its proforma, bank permit and declaration |
| 2 | **Documents** | Proforma invoice, commercial invoice, packing list and truck waybill (Central Hub design) and purchase order (Baraka design); PDF download and print version |
| 3 | **FCY & bank permit** | 9-step workflow per proforma: FCY request, insurance, FCY approval, submission of proforma and PO, bank PO approval, chamber documents, DHL to vendor, bank permit |
| 4 | **Declarations** | 180-day rule from bank permit approval; days remaining, due-soon and delinquent alerts; batch submission |

Across the system:

- **Edit and Delete** on every record. Deleting takes two clicks and keeps links clean (for example, deleting a proforma unlinks its containers).
- **Backup** (header button): download a full backup file (.json) or an Excel copy (.xlsx), and restore from a backup file. The button shows a reminder when there has been no backup for 30 days.
- **Connected data:** proformas can be created from order plan containers; the bank PO no and date from the FCY workflow fill the commercial invoice and packing list (invoice date = bank PO date + 1 day); a bank permit issued in the FCY workflow is linked to that proforma's containers.

## Technology

| Layer | Technology |
|-------|-----------|
| Front end | One self-contained `index.html`: HTML5, CSS, plain JavaScript (no framework, no build step) |
| Fonts | IBM Plex Sans (Google Fonts) |
| PDF | [html2pdf.js 0.10.1](https://cdnjs.com/libraries/html2pdf.js) (html2canvas + jsPDF) from cdnjs |
| Excel export | [SheetJS 0.18.5](https://cdnjs.com/libraries/xlsx) from cdnjs, loaded only when the Excel copy is requested |
| Images | Letterheads and stamp embedded as data URIs |
| Back end | Claude artifact runtime: `claude.use("db")` (real-time JSON document store) and `claude.use("downloads")` (file saving) |

`index.html` contains six script blocks: Documents, Order plan, FCY & bank permit, Declarations, Backup, and the module switcher.

## Running it

### On claude.ai (current setup)

The system runs as a published Claude artifact, and its data is stored there. To publish a changed version, give Claude the updated `index.html` in a conversation and ask it to publish to the existing artifact link, so the saved data is kept. Staff need Claude accounts in the same Team or Enterprise organization; share the artifact with **Edit** access for data entry and **View** access for managers.

### On Firebase or another server

Outside claude.ai, `window.claude` does not exist. The page opens, but saving, loading and downloads are disabled until the back end is replaced. A ready Firebase version (staff sign-in, security rules, data import and staff tools) is kept separately; see [docs/deployment.md](docs/deployment.md).

## Updating this repository

1. Replace `index.html` with the new version (GitHub: **Add file → Upload files**, then **Commit changes**).
2. Add a line to [CHANGELOG.md](CHANGELOG.md).

## Documentation

- [docs/data-model.md](docs/data-model.md): collections and fields
- [docs/workflow.md](docs/workflow.md): business rules built into the system
- [docs/backup.md](docs/backup.md): how data is saved, backed up and restored
- [docs/deployment.md](docs/deployment.md): moving to Firebase, PocketBase or your own server
- [CHANGELOG.md](CHANGELOG.md): what changed in each version
